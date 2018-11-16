依赖注入\(DI\)是一个对象定义其依赖关系的过程，也就是说，对象只能通过构造函数参数、工厂方法的参数或在对象实例被构造或从工厂方法返回后设置的属性来定义它们所工作的其他对象。然后容器在创建bean时注入这些依赖项。这个过程基本上是反向的，因此名称Inversion of Control（IoC），bean本身通过使用类的直接构造或服务定位器模式来控制其依赖项的实例化或位置。

使用DI原则的代码更简洁，当对象具有依赖关系时，解耦会更有效。该对象不查找其依赖项，也不知道依赖项的位置或类。因此，类变得更容易测试，特别是当依赖关系在接口或抽象基类上时，这允许在单元测试中使用存根或模拟实现。

DI存在两种主要变体，[Constructor-based dependency injection](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-constructor-injection)和[Setter-based dependency injection](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-setter-injection)。

#### Constructor-based dependency injection（基于构造函数的依赖注入）

.基于构造函数的DI由容器调用具有多个参数的构造函数来完成，每个参数表示一个依赖项。调用具有特定参数的静态工厂方法来构造bean几乎是等效的，本讨论同样处理构造函数和静态工厂方法的参数。以下示例显示了一个只能通过构造函数注入进行依赖注入的类。请注意，此类没有什么特别之处，它是一个POJO，它不依赖于容器特定的接口，基类或注释。

```
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on a MovieFinder
    private MovieFinder movieFinder;

    // a constructor so that the Spring container can inject a MovieFinder
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```

##### Constructor argument resolution（构造函数参数解析）

使用参数的类型进行构造函数参数解析匹配。如果bean定义的构造函数参数中不存在潜在的歧义，那么在bean定义中定义构造函数参数的顺序是在实例化bean时将这些参数提供给适当的构造函数的顺序。 考虑以下课程：

```
package x.y;

public class Foo {

    public Foo(Bar bar, Baz baz) {
        // ...
    }
}
```

假设`Bar`和`Baz`类与继承无关，则不存在潜在的歧义。因此，以下配置工作正常，您无需在&lt;constructor-arg /&gt;元素中显式指定构造函数参数索引或类型。

```
<beans>
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
    </bean>

    <bean id="bar" class="x.y.Bar"/>

    <bean id="baz" class="x.y.Baz"/>
</beans>
```

当引用另一个bean时，类型是已知的，并且可以进行匹配（与前面的示例一样）。当使用简单类型时，例如&lt;value&gt; true &lt;/ value&gt;，Spring无法确定类型 值，因此无法在没有帮助的情况下按类型匹配。 考虑以下课程：

```
package examples;

public class ExampleBean {

    // Number of years to calculate the Ultimate Answer
    private int years;

    // The Answer to Life, the Universe, and Everything
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

在前面的场景中，如果使用type属性显式指定构造函数参数的类型，则容器可以使用与简单类型匹配的类型。

例如：

```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```

使用index属性显式指定构造函数参数的索引。 例如：

```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```

除了解决多个简单值的歧义之外，指定索引还可以解决构造函数具有相同类型的两个参数的歧义。请注意，索引从0开始。

您还可以使用构造函数参数名称进行值消歧：

```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```

请记住，要使这项工作开箱即用，必须在启用调试标志的情况下编译代码，以便Spring可以从构造函数中查找参数名称。如果您无法使用调试标志编译代码（或者不想）你可以使用@ConstructorProperties JDK注释来显式命名你的构造函数参数。 然后，示例类必须如下所示：

```
package examples;

public class ExampleBean {

    // Fields omitted

    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

#### Setter-based dependency injection（基于Setter的依赖注入）

基于setter的DI是在调用无参数构造函数或无参数静态工厂方法来实例化bean之后，通过容器调用bean上的setter方法来完成的。

以下示例显示了一个只能使用纯setter注入进行依赖注入的类。这个类是传统的Java。它是一个POJO，它不依赖于容器特定的接口，基类或注释。

```
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on the MovieFinder
    private MovieFinder movieFinder;

    // a setter method so that the Spring container can inject a MovieFinder
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```

`ApplicationContext`支持它管理的bean是基于构造函数和基于setter的DI。 在通过构造函数方法注入了一些依赖项之后，它还支持基于setter的DI。您可以以`BeanDefinition`的形式配置依赖项，并将其与`PropertyEditor`实例结合使用，以将属性从一种格式转换为另一种格式。但是，大多数Spring用户不直接使用这些类（即以编程方式），而是使用XML bean定义或带注释的组件（即使用`@ Component`，`@ Controller`等注释的类）或基于Java的在`@Configuration`类里使用`@Bean`方法。然后，这些源在内部转换为BeanDefinition的实例，并用于加载整个Spring IoC容器实例。

> 基于构造函数或基于setter的DI？
>
> 由于您可以混合基于构造函数和基于setter的DI，因此使用构造函数处理强制依赖关系和setter方法或配置方法处理可选依赖关系是一个很好的经验法则。请注意，在setter方法上使用@Required注释可用于使属性成为必需的依赖项。
>
> Spring团队通常提倡构造函数注入，因为它可以将应用程序组件实现为不可变对象，并确保所需的依赖项不为null。此外，构造函数注入的组件始终以完全初始化状态返回到客户端（调用）代码。作为附注，大量的构造函数参数是一个糟糕的代码气味，暗示该类可能有太多的责任，应该重构以更好地解决关注点的正确分离。
>
> Setter注入应主要仅用于可在类中指定合理默认值的可选依赖项。 否则，必须在代码使用依赖项的任何位置执行非空检查。 setter注入的一个好处是setter方法使该类的对象可以在以后重新配置或重新注入。因此，通过JMX MBean进行管理是setter注入的一个引人注目的用例。
>
> 使用对特定类最有意义的DI样式。有时，在处理您没有第三方类的源码时，您会选择。 例如，如果第三方类没有公开任何setter方法，那么构造函数注入可能是唯一可用的DI形式。

#### Dependency resolution process（依赖性解析过程）

容器执行bean依赖性解析，如下所示：

* ApplicationContext是使用描述所有bean的配置元数据创建和初始化的。可以通过XML，Java代码或注释指定配置元数据。
* 对于每个bean，如果使用的是依赖于普通构造函数的，那么它的依赖关系将以属性，构造函数参数或static-factory方法的参数的形式表示。 实际创建bean时，会将这些依赖项提供给bean。
* 每个属性或构造函数参数都是要设置实际定义的值，或者是对容器中另一个bean的引用。
* 作为值的每个属性或构造函数参数都从其指定格式转换为该属性或构造函数参数的实际类型。默认Spring可以将以字符串格式提供的值转换为所有内置类型，例如int，long， 字符串，布尔值等

在创建容器时，Spring容器验证每个bean的配置。然而，直到真正创建bean时，才会设置bean属性本身。当创建容器时，将创建单个作用域并设置为**预实例化**的bean\(默认\)。定义作用域 参阅[Section 7.5, “Bean scopes”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes)。 否则，仅在**请求时才创建**bean。创建bean可能会导致创建bean的图，因为创建和分配了bean的依赖项及其依赖项\(等等\)。注意，这些依赖项之间的解析不匹配可能出现得较晚，即在第一次创建受影响的bean时。

```
Circular dependencies（循环依赖）

如果您主要使用构造函数注入，可能出现创建无法解析的循环依赖关系场景。

例如：A类需要通过构造函数注入实现B类实例，而B类需要通过构造函数注入实现A类实例。 如果为A类和B类配置bean以便相互注入，则Spring IoC
容器会在运行时检测此循环引用，并抛出BeanCurrentlyInCreationException。

一种可能的解决方案是编辑由setter而不是构造函数配置的某些类的源代码。 或者，避免构造函数注入并仅使用setter注入。
换句话说，虽然不建议使用，但您可以使用setter注入配置循环依赖项。

与典型情况（没有循环依赖）不同，bean A和bean B之间的循环依赖强制其中一个bean在完全初始化之前被注入另一个bean（一个经典的鸡/蛋场景）。
```



