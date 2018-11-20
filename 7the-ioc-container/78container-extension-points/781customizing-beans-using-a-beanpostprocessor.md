`BeanPostProcessor`接口定义了可以实现的回调方法，以提供您自己的（或覆盖容器的默认）实例化逻辑，依赖关系解析逻辑等。如果要在Spring容器完成实例化，配置和初始化bean之后实现某些自定义逻辑，则可以插入一个或多个BeanPostProcessor实现。

您可以配置多个BeanPostProcessor实例，并且可以通过设置order属性来控制这些BeanPostProcessors的执行顺序。仅当BeanPostProcessor实现Ordered接口时，才能设置此属性；如果您编写自己的BeanPostProcessor，则应考虑实现Ordered接口。有关更多详细信息，请参阅BeanPostProcessor和Ordered接口的javadoc。另请参阅下面有关BeanPostProcessors编程注册[programmatic registration of](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-programmatically-registering-beanpostprocessors)的说明。

> beanpostprocessor对bean\(或对象\)实例进行操作;也就是说，Spring IoC容器实例化一个bean实例，然后beanpostprocessor执行它们的工作。
>
> BeanPostProcessors的范围是每个容器。这仅在您使用容器层次结构时才相关。如果在一个容器中定义BeanPostProcessor，它将只对该容器中的bean进行后处理。换句话说，在一个容器中定义的bean不会被另一个容器中定义的BeanPostProcessor进行后处理，即使两个容器都是同一层次结构的一部分。
>
> 要更改实际的bean定义（即定义bean的计划），您需要使用BeanFactoryPostProcessor，如[Section 7.8.2, “Customizing configuration metadata with a BeanFactoryPostProcessor”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-extension-factory-postprocessors)



