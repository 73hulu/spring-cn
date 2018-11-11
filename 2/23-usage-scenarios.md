前面描述的构建块使Spring在许多场景中成为一种逻辑选择，从在资源受限的设备上运行的嵌入式应用程序到使用Spring事务管理功能和web框架集成的成熟企业应用程序。

图2.2。典型的成熟的Spring web应用程序

![](/assets/overview-full.png)

Spring的声明性事务管理特性使web应用程序完全具有事务性，就像使用EJB容器管理的事务一样。所有定制的业务逻辑都可以用简单的pojo实现，并由Spring的IoC容器管理。其他服务包括对发送电子邮件的支持和独立于web层的验证，web层允许您选择在何处执行验证规则。Spring的ORM支持集成了JPA、Hibernate和JDO;例如，在使用Hibernate时，您可以继续使用现有的映射文件和标准的Hibernate SessionFactory配置。表单控制器无缝地将web层与域模型集成，从而消除了ActionForms或其他将HTTP参数转换为域模型值的类的需求。

图2.3。使用第三方web框架的Spring中间层

![](/assets/overview-thirdparty-web.png)

有时情况不允许您完全切换到另一个框架。Spring框架并不强制您使用它的所有内容;这不是一种孤注一掷的解决方案。使用Struts、Tapestry、JSF或其他UI框架构建的现有前端可以与基于Spring的中间层集成，这允许您使用Spring事务特性。您只需使用ApplicationContext连接业务逻辑，并使用WebApplicationContext集成您的业务逻辑

图2.4。Remoting使用场景

![](/assets/overview-remoting.png)

当您需要通过web services访问现有代码时，您可以使用Spring的Hessian-、Burlap-、Rmi-或JaxRpcProxyFactory类。启用对现有应用程序的远程访问并不困难。

图2.5 EJB-包装现有pojo

![](/assets/overview-ejb.png)

Spring框架还为企业javabean提供了一个访问和抽象层，使您能够重用现有的pojo并将它们包装在无状态会话bean中，以便在可能需要声明性安全性的可伸缩、故障安全的web应用程序中使用。

