核心容器包括：`spring-core`, `spring-beans`,`spring-context`, `spring-context-support`, and `spring-expression` \(Spring Expression Language\) modules.

`spring-core`和`spring-beans`模块[provide the fundamental parts of the framework](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-introduction)提供框架的基本部分，包括了IOC和依赖注入特性。`BeanFactory`是工厂模式的复杂实现。它消除了对编程单例的需求，并允许您将依赖项的配置和规范与实际的程序逻辑分离开来。

`spring-context`模块构建在`spring-core`, `spring-beans` 模块之上：它是一种以类似于JNDI注册中心的框架样式方式访问对象的方法。`spring-context`模块从`spring-beans模块`继承其特性，并添加对国际化\(例如，资源包\)、事件传播、资源加载以及通过\(例如Servlet容器\)透明创建上下文的支持。`spring-context`模块还支持Java EE特性，如EJB、JMX和基本远程。`ApplicationContext`接口是`spring-context`模块的焦点。`spring-context-support`提供了将公共第三方库集成到Spring应用程序上下文中的支持，用于缓存\(EhCache、Guava、JCache\)、邮件\(JavaMail\)、调度\(CommonJ、Quartz\)和模板引擎\(FreeMarker、JasperReports、Velocity\)。

`spring-expression`模块提供了一种强大的表达式语言[_Expression Language_](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#expressions)，用于在运行时查询和操作对象图。它是JSP 2.1规范中指定的统一表达式语言\(unified expression language, unified EL\)的扩展。它还支持列表投影和选择以及常见的列表聚合



