部署到Servlet 2.5服务仍然是可选的，但是Spring Framework 4.0现在主要关注Servlet 3.0+环境。如果您正在使用Spring MVC测试框架（[Spring MVC Test Framework](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#spring-mvc-test-framework)），您将需要确保Servlet 3.0兼容的JAR在您的测试类路径中。

除了后面提到的WebSocket支持之外，Spring的Web模块还进行了以下一般性的改进:

* 您可以在Spring MVC应用程序中使用新的`@RestController`注释，从而无需向每个`@RequestMapping`方法添加`@ResponseBody`。
* 添加了AsyncRestTemplate类，允许在开发REST客户机时提供非阻塞异步支持（[allowing non-blocking asynchronous support](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#rest-async-resttemplate)）。
* Spring现在在开发Spring MVC应用程序时提供了全面的时区支持（[comprehensive timezone support](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#mvc-timezone)）。



