`request`, `session`, `globalSession`, `application`, 和`websocket`作用域仅在您使用Web感知的Spring ApplicationContext实现（例如XmlWebApplicationContext）时才可用。如果您将这些作用域与常规的Spring IoC容器\(如ClassPathXmlApplicationContext\)一起使用，则会抛出一个IllegalStateException来表示一个未知的bean作用域。

#### Initial web configuration----初始化web配置

要支持`request`, `session`, `globalSession`, `application`, 和`websocket`级别（Web范围的bean）支持bean的范围，在定义bean之前需要一些小的初始配置。\(对于标准的作用域、单例对象和原型，不需要在此初始设置。\)

如何完成此初始设置取决于您的特定Servlet环境。

如果您在Spring Web MVC中访问作用域bean，那么实际上，在由Spring DispatcherServlet或DispatcherPortlet处理的请求中，不需要特殊设置：DispatcherServlet和DispatcherPortlet已经公开了所有相关状态。

如果使用Servlet 2.5 web容器，在Spring的DispatcherServlet之外处理请求\(例如，在使用JSF或Struts时\)，则需要注册`org.springframework.web.context.request.RequestContextListener   ServletRequestListener`。对于Servlet 3.0+，这可以通过WebApplicationInitializer接口以编程方式完成。或者，对于较旧的容器，向web应用程序的web.xml添加以下声明：

```
<web-app>
    ...
    <listener>
        <listener-class>
            org.springframework.web.context.request.RequestContextListener
        </listener-class>
    </listener>
    ...
</web-app>
```

或者，如果您的侦听器设置存在问题，请考虑使用Spring的RequestContextFilter。过滤器映射取决于周围的Web应用程序配置，因此您必须根据需要进行更改。

```
<web-app>
    ...
    <filter>
        <filter-name>requestContextFilter</filter-name>
        <filter-class>org.springframework.web.filter.RequestContextFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>requestContextFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ...
</web-app>
```

DispatcherServlet, RequestContextListener, 和RequestContextFilter所有都做同样的事情，即将HTTP请求对象绑定到为该请求提供服务的Thread。这使得请求和会话范围的bean可以在调用链的下游进一步使用。

