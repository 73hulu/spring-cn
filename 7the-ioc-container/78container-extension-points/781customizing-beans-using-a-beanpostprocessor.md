`BeanPostProcessor`接口定义了可以实现的回调方法，以提供您自己的（或覆盖容器的默认）实例化逻辑，依赖关系解析逻辑等。如果要在Spring容器完成实例化，配置和初始化bean之后实现某些自定义逻辑，则可以插入一个或多个BeanPostProcessor实现。

您可以配置多个BeanPostProcessor实例，并且可以通过设置order属性来控制这些BeanPostProcessors的执行顺序。仅当BeanPostProcessor实现Ordered接口时，才能设置此属性；如果您编写自己的BeanPostProcessor，则应考虑实现Ordered接口。有关更多详细信息，请参阅BeanPostProcessor和Ordered接口的javadoc。另请参阅下面有关BeanPostProcessors编程注册[programmatic registration of](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-programmatically-registering-beanpostprocessors)的说明。

> beanpostprocessor对bean\(或对象\)实例进行操作;也就是说，Spring IoC容器实例化一个bean实例，然后beanpostprocessor执行它们的工作。
>
> BeanPostProcessors的范围是每个容器。这仅在您使用容器层次结构时才相关。如果在一个容器中定义BeanPostProcessor，它将只对该容器中的bean进行后处理。换句话说，在一个容器中定义的bean不会被另一个容器中定义的BeanPostProcessor进行后处理，即使两个容器都是同一层次结构的一部分。
>
> 要更改实际的bean定义（即定义bean的计划），您需要使用BeanFactoryPostProcessor，如[Section 7.8.2, “Customizing configuration metadata with a BeanFactoryPostProcessor”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-extension-factory-postprocessors)

org.springframework.beans.factory.config.BeanPostProcessor接口恰好包含两个回调方法。当这样一个类注册为容器的后处理器，容器创建每个bean实例，后处理器从容器得到一个回调容器在初始化方法之前\(如InitializingBean afterPropertiesSet\(\)和任何宣布init方法\)以及任何bean初始化回调之后都会被调用。后处理器可以对bean实例执行任何操作，包括完全忽略回调。 bean后处理器通常检查回调接口或者可以用代理包装bean。一些Spring AOP基础结构类实现为bean后处理器，以便提供代理包装逻辑。

`ApplicationContext`自动检测在实现bean后处理器接口的配置元数据中定义的任何bean。`ApplicationContext`将这些bean注册为后处理器，以便稍后在创建bean时调用它们。Bean后处理器可以像任何其他bean一样部署在容器中。

请注意，在配置类上使用`@Bean`工厂方法声明`BeanPostProcessor`时，工厂方法的返回类型应该是实现类本身，或者至少是`org.springframework.beans.factory.config.BeanPostProcessor`接口，清楚地表明 该bean的后处理器性质。否则，`ApplicationContext`将无法在完全创建之前按类型自动检测它。由于`BeanPostProcessor`需要尽早实例化以便应用于上下文中其他bean的初始化，因此这种早期类型检测至关重要。

