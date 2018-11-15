bean定义本质上是用于创建一个或多个对象的配方。容器在被询问时查看命名bean的配方，并使用由该bean定义封装的配置元数据来创建（或获取）实际对象。

如果使用基于XML的配置元数据，则指定要在&lt;bean /&gt;元素的class属性中实例化的对象的类型（或类）。此类属性通常是必需的，它在内部是BeanDefinition实例上的Class属性。（有关异常，请参阅[the section called “Instantiation using an instance factory method”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-class-instance-factory-method)和[Section 7.7, “Bean definition inheritance”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-child-bean-definitions)）。您可以通过以下两种方式之一使用Class属性：

* 通常，在容器本身通过反射调用其构造函数直接创建指定bean类，稍微等同于java代码使用new操作。
* 以指定包含静态工厂方法的实际类，在不太常见的情况下，容器在类上调用静态工厂方法来创建bean。从静态工厂方法调用返回的对象类型可能完全是同一个类或另一个类。

```
Inner class names（内部类）. 如果要为静态嵌套类配置bean定义，则必须使用嵌套类的二进制名称。

例如，如果在com.example包中有一个名为Foo的类，并且这个Foo类有一个名为Bar的静态嵌套类，那么bean定义中'class'属性的值将是......

com.example.Foo$Bar

请注意，在名称中使用$字符可以将嵌套类名与外部类名分开。
```



