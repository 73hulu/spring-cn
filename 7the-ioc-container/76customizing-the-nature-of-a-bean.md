除了上面讨论的ApplicationContextAware和BeanNameAware之外，Spring还提供了一系列Aware接口，允许bean向容器指示它们需要某种基础结构依赖性。最重要的Aware接口总结如下 - 作为一般规则，名称是依赖类型的良好指示：

表7.4  Aware接口

| Name | Injected Dependency  注入依赖 | Explained in…​   解释在...... |
| :--- | :--- | :--- |
| ApplicationContextAware | 声明ApplicationContext | [Section 7.6.2, “ApplicationContextAware and BeanNameAware”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-aware) |
| ApplicationEventPublisherAware | 随附的ApplicationContext的事件发布者 | [Section 7.15, “Additional capabilities of the ApplicationContext”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#context-introduction) |
| BeanClassLoaderAware | 用于加载bean类的类加载器 | [Section 7.3.2, “Instantiating beans”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-class) |
| BeanFactoryAware | 声明BeanFactory | [Section 7.6.2, “ApplicationContextAware and BeanNameAware”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-aware) |
| BeanNameAware | 声明bean的名称 | [Section 7.6.2, “ApplicationContextAware and BeanNameAware”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-aware) |
| BootstrapContextAware | 容器运行的资源适配器BootstrapContext。通常仅在JCA感知的ApplicationContexts中可用 | [Chapter 32, JCA CCI](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#cci) |
| LoadTimeWeaverAware | 定义的weaver用于在加载时处理类定义 | [Section 11.8.4, “Load-time weaving with AspectJ in the Spring Framework”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#aop-aj-ltw) |
| MessageSourceAware | 用于解析消息的已配置策略（支持参数化和国际化） | [Section 7.15, “Additional capabilities of the ApplicationContext”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#context-introduction) |
| NotificationPublisherAware | Spring JMX通知发布者 | [Section 31.7, “Notifications”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#jmx-notifications) |
| PortletConfigAware | 容器运行的当前PortletConfig。仅在Web感知的Spring ApplicationContext中有效 | [Chapter 25, Portlet MVC Framework](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#portlet) |
| PortletContextAware | 容器运行的当前PortletContext。仅在Web感知的Spring ApplicationContext中有效 | [Chapter 25, Portlet MVC Framework](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#portlet) |
| ResourceLoaderAware | 配置的加载程序，用于对资源进行低级访问 | [Chapter 8, Resources](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#resources) |
| ServletConfigAware | 当前ServletConfig容器运行。仅在Web感知的Spring ApplicationContext中有效 | [Chapter 22, Web MVC framework](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#mvc) |
| ServletContextAware | 容器运行的当前ServletContext。仅在Web感知的Spring ApplicationContext中有效 | [Chapter 22, Web MVC framework](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#mvc) |



