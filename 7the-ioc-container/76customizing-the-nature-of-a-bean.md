除了上面讨论的ApplicationContextAware和BeanNameAware之外，Spring还提供了一系列Aware接口，允许bean向容器指示它们需要某种基础结构依赖性。最重要的Aware接口总结如下 - 作为一般规则，名称是依赖类型的良好指示：

表7.4  Aware接口

| Name | Injected Dependency  注入依赖 | Explained in…​   解释在...... |
| :--- | :--- | :--- |
| ApplicationContextAware | 声明ApplicationContext | [Section 7.6.2, “ApplicationContextAware and BeanNameAware”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-aware) |
| ApplicationEventPublisherAware | 随附的ApplicationContext的事件发布者 | [Section 7.15, “Additional capabilities of the ApplicationContext”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#context-introduction) |
| BeanClassLoaderAware | 用于加载bean类的类加载器 | [Section 7.3.2, “Instantiating beans”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-class) |
| BeanFactoryAware | 声明BeanFactory | [Section 7.6.2, “ApplicationContextAware and BeanNameAware”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-aware) |



