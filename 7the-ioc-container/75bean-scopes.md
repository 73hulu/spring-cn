当您创建一个bean定义时，您将创建一个用于创建由该bean定义的类的实际实例的配方。bean定义是一个配方的思想很重要，因为它意味着，与一个类一样，您可以从一个配方创建许多对象实例。

您不仅可以控制要插入到从特定bean定义创建的对象中的各种依赖项和配置值，还可以控制从特定bean定义创建的对象的范围。这种方法功能强大且灵活，您可以选择通过配置创建的对象的范围，而不必在Java类级别烧固对象的范围。可以将Bean定义为部署在多个范围之一中：Spring框架开箱即用支持7个作用域，其中5个范围仅在您使用Web感知的ApplicationContext时才可用。

下面的作用域是开箱即用的。您还可以创建自定义范围[a custom scope](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-custom)。

表7.3  Bean范围

| Scope | Description |
| :--- | :--- |
| [singleton](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-singleton) | （默认）将每个Spring IoC容器的单个bean定义范围限定为单个对象实例。 |
| [prototype](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-prototype) | 将单个bean定义范围限定为任意数量的对象实例。 |
| [request](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-request) | 将单个bean定义范围限定为单个HTTP请求的生命周期; 也就是说，每个HTTP请求都有自己的bean实例，它是在单个bean定义的后面创建的。 仅在Web感知Spring ApplicationContext的上下文中有效。 |
| [session](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-session) | 将单个bean定义范围限定为HTTP session的生命周期。 仅在Web感知Spring ApplicationContext的上下文中有效 |
| [globalSession](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-global-session) | 将单个bean定义范围限定为全局HTTP会话的生命周期。 通常仅在Portlet上下文中使用时有效。 仅在Web感知Spring ApplicationContext的上下文中有效。 |
| [application](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes-application) | 将单个bean定义范围限定为ServletContext的生命周期。 仅在Web感知Spring ApplicationContext的上下文中有效。 |
| [websocket](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#websocket-stomp-websocket-scope) | 将单个bean定义范围限定为WebSocket的生命周期。 仅在Web感知Spring ApplicationContext的上下文中有效。 |



