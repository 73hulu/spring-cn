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

#### Request scope

考虑以下bean定义的XML配置:

```
<bean id="loginAction" class="com.foo.LoginAction" scope="request"/>
```

Spring容器通过对每个HTTP request使用loginAction bean定义来创建LoginAction bean的新实例。也就是说，loginAction bean的作用域是在HTTP request级别。您可以随意更改创建的实例的内部状态，因为从相同的loginAction bean定义创建的其他实例不会看到状态的变化;它们是特定于单个请求的。当请求完成处理时，作用域为该请求的bean将被丢弃。

使用注释驱动的组件或Java Config时，可以使用`@RequestScope`注释将组件分配给request scope。

```
@RequestScope
@Component
public class LoginAction {
    // ...
}
```

#### Session scope

考虑以下bean定义的XML配置:

```
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>
```

Spring容器通过在单个HTTP  Session的生存期内使用userPreferences bean定义来创建UserPreferences bean的新实例。换句话说，userPreferences bean在HTTP Session级别有效地作用域。与request范围的bean一样，您可以根据需要更改创建的实例的内部状态，因为知道同样使用从同一userPreferences bean定义创建的实例的其他HTTP Session实例在状态中看不到这些更改 ，因为它们特定于单个HTTP会话。当HTTP Session最终被丢弃时，作用于特定HTTP Session的bean也被丢弃。

在使用注释驱动组件或Java配置时，可以使用@SessionScope注释将组件分配给Session scope。

```
@SessionScope
@Component
public class UserPreferences {
    // ...
}
```

#### Global session scope

考虑以下bean定义的XML配置：

```
<bean id="userPreferences" class="com.foo.UserPreferences" scope="globalSession"/>
```

globalSession作用域类似于标准HTTP Session 作用域（如上所述），仅适用于基于portlet的Web应用程序的上下文。portlet规范定义了构成单个portlet Web应用程序的所有portlet之间共享的全局Session的概念。在globalSession作用域中定义的bean的作用域\(或绑定\)限于全局portlet会话的生命周期。如果编写基于Servlet的标准Web应用程序并将一个或多个bean定义为具有globalSession作用域，则使用标准HTTP `Session`作用域，并且不会引发错误。

#### Application scope

考虑以下bean定义的XML配置：

```
<bean id="appPreferences" class="com.foo.AppPreferences" scope="application"/>
```

Spring容器通过对整个Web应用程序使用appPreferences bean定义一次来创建AppPreferences bean的新实例。也就是说，appPreferences bean的作用域是ServletContext级别，存储为常规的ServletContext属性。这有点类似于Spring单例bean，但在两个重要方面有所不同：它是每个ServletContext的单例，而不是每个Spring的'ApplicationContext'（在任何给定的Web应用程序中可能有几个），它实际上是公开的，因此可以看作ServletContext属性。

使用注释驱动的组件或Java Config时，可以使用@ApplicationScope注释将组件分配给应用程序范围。

```
@ApplicationScope
@Component
public class AppPreferences {
    // ...
}
```

#### Scoped beans as dependencies

Spring IoC容器不仅管理对象（bean）的实例化，还管理协作者（或依赖关系）的连接。如果要将（例如）HTTP请求作用域bean注入到具有较长寿命范围的另一个bean中，您可以选择注入AOP代理来代替作用域bean。也就是说，您需要注入一个代理对象，该代理对象公开与作用域对象相同的公共接口，但它也可以从相关的作用域\(例如HTTP请求\)检索实际目标对象，并将方法调用委托给实际对象。

