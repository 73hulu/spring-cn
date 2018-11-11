Spring框架是一种轻量级的解决方案，是构建企业级应用的一站式服务。然而，Spring是一个模块化，允许只引入需要的部分，而不用引入其他部分。您可以使用IoC容器，上面有任何web框架，但也只能使用Hibernate集成代码或JDBC抽象层。Spring框架支持声明式事务、通过RMI或web服务远程访问逻辑，以及各种持久化数据的选项。它提供功能全面的MVC框架，并使您能够透明地将AOP集成到您的软件中。



Spring被设计为非侵入式，意味着你的主要逻辑代码不需要依赖框架本身。在集成层\\(如数据访问层\\)中，将存在对数据访问技术和Spring库的依赖。但是，将这些依赖项与代码库的其他部分隔离开来应该很容易。



本文是Spring框架特性的参考指南。如果您对该文档有任何要求、评论或问题，请将它们发布到\[用户邮件列表\]\(https://groups.google.com/forum/\#!forum/spring-framework-contrib\)中。关于框架本身的问题应该在\[StackOverflow\]\(https://spring.io/questions\)上问\\\\(参见https://spring.io/questions\\)。

