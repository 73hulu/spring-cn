bean定义本质上是用于创建一个或多个对象的配方。容器在被询问时查看命名bean的配方，并使用由该bean定义封装的配置元数据来创建（或获取）实际对象。

如果使用基于XML的配置元数据，则指定要在&lt;bean /&gt;元素的class属性中实例化的对象的类型（或类）。此类属性通常是必需的，它在内部是BeanDefinition实例上的Class属性。（有关异常，请参阅[the section called “Instantiation using an instance factory method”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-class-instance-factory-method)和[Section 7.7, “Bean definition inheritance”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-child-bean-definitions)）。您可以通过以下两种方式之一使用Class属性：

* 通常，在容器本身通过反射调用其构造函数直接创建指定bean类，稍微等同于java代码使用new操作。
* 以指定包含静态工厂方法的实际类，在不太常见的情况下，容器在类上调用静态工厂方法来创建bean。从静态工厂方法调用返回的对象类型可能完全是同一个类或另一个类。

```
Inner class names（内部类）. 如果要为静态嵌套类配置bean定义，则必须使用嵌套类的二进制名称。

例如，如果在com.example包中有一个名为Foo的类，并且这个Foo类有一个名为Bar的静态嵌套类，那么bean定义中'class'属性的值将是......

com.example.Foo$Bar

请注意，在名称中使用$字符可以将嵌套类名与外部类名分开。
```

#### Instantiation with a constructor（使用构造函数实例化）

当您通过构造方法创建bean时，所有普通类都可以使用并与Spring兼容。也就是说，正在开发的类不需要实现任何特定接口或以特定方式编码。简单地指定bean类就足够了。 但是，根据您为该特定bean使用的IoC类型，您可能需要一个默认（空）构造函数。

Spring IoC容器几乎可以管理您希望它管理的任何类; 它不仅限于管理真正的JavaBeans。大多数Spring用户更喜欢实际的JavaBeans，只有一个默认（无参数）构造函数，并且在容器中的属性之后建模了适当的setter和getter。你也可以在你的容器里拥有更多奇异的非bean风格的classes。例如，如果您需要使用绝对不符合JavaBean规范的旧连接池，那么Spring也可以对其进行管理。

使用基于XML的配置元数据，您可以按如下方式指定bean类：

```
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
```

有关为构造函数提供参数的机制（如果需要）以及在构造对象后设置对象实例属性的详细信息，请参阅[Injecting Dependencies](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-collaborators)。

#### Instantiation with a static factory method（使用静态工厂方法实例化）

定义使用静态工厂方法创建的bean时，可以使用class属性指定包含静态工厂方法的类和名为factory-method的属性，以指定工厂方法本身的名称。您应该能够调用此方法（使用后面描述的可选参数）并返回一个活动对象，随后将其视为通过构造函数创建的对象。对于此类bean定义的一个用途是在遗留系统中调用静态工厂代码。

以下bean定义指定通过调用factory-method创建bean。该定义未指定返回对象的类型（类），仅指定包含工厂方法的类。在此示例中，createInstance（）方法必须是静态方法。

```
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```

```
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```

有关向工厂方法提供（可选）参数以及在从工厂返回对象后设置对象实例属性的机制的详细信息，请参阅[Dependencies and configuration in detail](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-properties-detailed)。

#### Instantiation using an instance factory method（使用实例工厂方法实例化）

类似于通过静态工厂方法实例化，使用实例工厂方法实例化从容器调用现有bean的非静态方法来创建新bean。要使用此机制，请将`class`属性保留为空，并在`factory-bean`属性中，在当前（或父/祖先）容器中指定bean的名称，该容器包含要调用以创建对象的实例方法。使用factory-method属性设置工厂方法本身的名称。

```
<!-- the factory bean, which contains a method called createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```

```
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
```

一个工厂类也可以包含多个工厂方法，如下所示：

```
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
```

```
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    private static AccountService accountService = new AccountServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }

    public AccountService createAccountServiceInstance() {
        return accountService;
    }
}
```

这种方法表明可以通过依赖注入（DI）来管理和配置工厂bean本身。 请参阅详细信息中的依赖关系和配置[Dependencies and configuration in detail](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-properties-detailed)。

> 在Spring文档中，factory bean指的是在Spring容器中配置的bean，它将通过实例或静态工厂方法创建对象。相比之下，FactoryBean（注意大小写）是指Spring特定的FactoryBean。



