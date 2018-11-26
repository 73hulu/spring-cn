> 注释是否比配置Spring的XML更好？
>
> 基于注释配置的引入引发了这种方法是否比XML更“好”的问题。简而言之，这要看情况而定。完整的回答是每种方式都有自己的有点和缺点，这要看开发者决定用哪种方式更适合自己。由于它的定义方式，注释在它们的声明中提供了很多上下文，从而导致更短和更简洁的配置。但是，XML擅长在不触及源代码或重新编译它们的情况下连接组件。一些开发者倾向于封闭的源代码，而另一些开发者排斥使用注解类，因为这样不再POJO，而且这样的配置方式变的不再集中管理并且很难控制。
>
> 无论选择如何，Spring都可以兼顾两种风格，甚至可以将它们混合在一起。值得一提的是，通过[JavaConfig](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java)方式，Spring允许以一种非侵入的方式使用注解，这样组件代码和工具就不会被侵入，通过[Spring Tool Suite](https://spring.io/tools/sts)支持全部的配置风格。

一种在 XML 与基于注解之间作取舍的衡量是依赖什么，是否能依赖于字节码元数据去装配组件以取代分类的 angle-bracket 的声明。开发人员不是使用XML来描述bean连接，而是通过在相关的类，方法或字段声明上使用注释将配置移动到组件类本身。如上所述[the section called “Example: The RequiredAnnotationBeanPostProcessor”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-extension-bpp-examples-rabpp)，将BeanPostProcessor与注释结合使用是扩展Spring IoC容器的常用方法。举个例子，在 spring 2.0 版本介绍了使用@Required注解去增强必要属性的需求。在 spring 2.5 版本可以使用相同的方式去驱动 spring 的依赖注入。从本质上来讲，@Autowired注解也能提供相似的功能，这被称之为“自动装配协作者” [Section 7.4.5, “Autowiring collaborators”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-autowire)，但他提供了更细粒度的控制以及更广泛的适用性。Spring 2.5还增加了对JSR-250注释的支持，例如@ PostConstruct和@PreDestroy。spring 3.0 增加了对 JSR-330（为 Java 而引的依赖注入）注解的支持，包含在 javax.jnject 包的注解，例如@Injectand和@Named。有关注解的更多信息可以在相关的章节中找到  [relevant section](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-standard-annotations).。

> 注意：注解注入会在 XML 注入之前执行，所以在装配的过程中，后者的配置将会覆盖前者的属性。

与往常一样，您可以将它们注册为单独的bean定义，但也可以通过在基于XML的Spring配置中包含以下标记来隐式注册它们（请注意包含`context`命名空间）：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```



