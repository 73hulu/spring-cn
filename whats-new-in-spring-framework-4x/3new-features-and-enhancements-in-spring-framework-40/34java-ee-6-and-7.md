 Java EE 6或更高版本现在被认为是Spring Framework 4的基线，JPA 2.0和Servlet 3.0规范具有特殊的相关性。为了与谷歌应用引擎和旧的应用服务器保持兼容，可以将Spring 4应用程序部署到Servlet 2.5环境中。但是，强烈推荐Servlet 3.0+，它是Spring测试和模拟包中用于开发环境中的测试设置的先决条件。

  


  


&gt;

 如果您是WebSphere 7用户，请确保安装JPA 2.0功能包。在WebLogic 10.3.4或更高版本上，安装附带的JPA 2.0补丁。这将把这两个服务器代都转换为Spring 4兼容的部署环境。

  


  


 更有前瞻性的是，Spring Framework 4.0现在支持Java EE 7级别的可应用规范:特别是JMS 2.0、JTA 1.2、JPA 2.1、Bean Validation 1.1和JSR-236并发实用工具。与往常一样，这种支持侧重于对这些规范的单独使用，例如在Tomcat或独立环境中。但是，当将Spring应用程序部署到Java EE 7服务器上时，它的工作效果同样良好。

  


  


 注意，Hibernate 4.3是JPA 2.1提供程序，因此只支持Spring Framework 4.0。与Bean验证1.1提供者一样，Hibernate验证器5.0也适用。Spring Framework 3.2并不支持这两种方法。

