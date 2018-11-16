在大多数应用程序场景中，容器中的大多数bean都是单例[singletons](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-singleton)。当单例bean需要与另一个单例bean协作，或者非单例bean需要与另一个非单例bean协作时，通常通过将一个bean定义为另一个bean的属性来处理依赖关系。当bean生命周期不同时会出现问题。假设单例bean A需要使用非单例（原型）bean B，可能是在A的每个方法上调用。容器只创建一次单独的bean A，因此只有一次机会来设置属性。每次需要bean B时，容器都不能向bean A提供新的bean B实例。

解决方案是放弃一些控制反转。通过实现ApplicationContextAware接口，并在每次bean A需要bean B实例时对容器进行getBean\(“B”\)调用，每次bean A需要时都要求（通常是新的）bean B实例。以下是此方法的示例：

```
// a class that uses a stateful Command-style class to perform some processing
package fiona.apple;

// Spring-API imports
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

public class CommandManager implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Object process(Map commandState) {
        // grab a new instance of the appropriate Command
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    protected Command createCommand() {
        // notice the Spring API dependency!
        return this.applicationContext.getBean("command", Command.class);
    }

    public void setApplicationContext(
            ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```

前面的内容是不可取的，因为业务代码知道并耦合到Spring Framework。Method Injection是Spring IoC容器的一个高级功能，它允许以干净的方式处理这个用例。

您可以阅读更多关于方法注入的动机 [this blog entry](https://spring.io/blog/2004/08/06/method-injection/)。

#### Lookup method injection----查找方法注入

Lookup方法注入是容器覆盖容器托管bean上的方法的能力，以返回容器中另一个命名bean的查找结果。查找通常涉及原型bean，如上一节中描述的场景。Spring Framework通过使用CGLIB库中的字节码生成来实现此方法注入，以动态生成覆盖该方法的子类。

> * 要使这个动态子类工作，Spring bean容器将子类化的类不能是final，并且要重写的方法也不能是final。
> * 对具有抽象方法的类进行单元测试需要您自己对类进行子类化，并提供抽象方法的存根实现。
> * 组件扫描也需要具体方法，这需要具体的类别来获取。
> * 另一个关键限制是，查找方法不能与工厂方法一起工作，特别是与配置类中的@Bean方法一起工作，因为容器在这种情况下不负责创建实例，因此不能动态创建运行时生成的子类。

查看前面代码片段中的CommandManager类，您会看到Spring容器将动态覆盖createCommand（）方法的实现。您的CommandManager类将不具有任何Spring依赖项，如重新编写的示例中所示：

```
package fiona.apple;

// no more Spring imports!

public abstract class CommandManager {

    public Object process(Object commandState) {
        // grab a new instance of the appropriate Command interface
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    // okay... but where is the implementation of this method?
    protected abstract Command createCommand();
}
```

在包含要注入的方法的客户机类（本例中为CommandManager）中，要注入的方法需要以下形式的签名：

```
<public|protected> [abstract] <return-type> theMethodName(no-arguments);
```

如果方法是抽象的，则动态生成的子类实现该方法。 否则，动态生成的子类将覆盖原始类中定义的具体方法。 例如：

```
<!-- a stateful bean deployed as a prototype (non-singleton) -->
<bean id="myCommand" class="fiona.apple.AsyncCommand" scope="prototype">
    <!-- inject dependencies here as required -->
</bean>

<!-- commandProcessor uses statefulCommandHelper -->
<bean id="commandManager" class="fiona.apple.CommandManager">
    <lookup-method name="createCommand" bean="myCommand"/>
</bean>
```

标识为commandManager的bean在需要myCommand bean的新实例时调用自己的方法createCommand（）。您必须小心将myCommand bean部署为原型，如果这实际上是需要的话。如果它是一个单例[singleton](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-singleton)，则每次都返回myCommand bean的相同实例。

或者，在基于注释的组件模型中，您可以通过@Lookup批注声明查找方法：

```
public abstract class CommandManager {

    public Object process(Object commandState) {
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup("myCommand")
    protected abstract Command createCommand();
}
```

或者，更具惯用性，您可以依赖于针对查找方法的声明返回类型解析目标bean：

```
public abstract class CommandManager {

    public Object process(Object commandState) {
        MyCommand command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup
    protected abstract MyCommand createCommand();
}
```



