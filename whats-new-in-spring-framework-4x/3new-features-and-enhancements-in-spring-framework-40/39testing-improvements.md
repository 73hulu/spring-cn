除了在`Spring -test`模块中删除废弃的代码之外，Spring Framework 4.0还引入了一些新的特性，用于单元测试和集成测试。

* `spring-test`模块中的几乎所有注释\(例如，`@ContextConfiguration`、`@WebAppConfiguration`、`@ context thierarchy`、`@ActiveProfiles`等等\)现在都可以用作元注释，用于创建自定义组合注释，并减少跨测试套件的配置重复。
* 现在可以通过编程方式解析活动bean定义概要文件，只需实现自定义`ActiveProfilesResolver`并通过`@ActiveProfiles`的解析器属性注册即可。
* `spring-core`模块中引入了一个新的SocketUtils类，它允许您扫描本地主机上的免费TCP和UDP服务器端口。这个功能并不是特定于测试，但是在编写需要使用套接字的集成测试时非常有用，例如启动内存中SMTP服务器、FTP服务器、Servlet容器等的测试。
* 在Spring 4.0中，`org.springframework.mock.web`中的一组模拟对象包现在基于Servlet 3.0 API。此外，Servlet API的一些模拟\(例如`MockHttpServletRequest`、`MockServletContext`等\)已经通过少量的增强和改进了可配置性进行了更新。



