如上图所示，Spring IoC容器使用一种配置元数据形式; 此配置元数据表示您作为应用程序开发人员如何告诉Spring容器在应用程序中实例化，配置和组装对象。

传统上，配置元数据以简单直观的XML格式提供，本章大部分内容用于传达Spring IoC容器的关键概念和功能。

> 基于XML的元数据不是唯一允许的配置元数据形式。Spring IoC容器本身完全与实际编写此配置元数据的格式分离。目前，许多开发人员为其Spring应用程序选择基于Java的配置[Java-based configuration](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java)。

有关在Spring容器中使用其他形式的元数据的信息，请参阅：

* [Annotation-based configuration](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-annotation-config)：Spring 2.5引入了对基于注释的配置元数据的支持。

* [Java-based configuration](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java)：从Spring 3.0开始，Spring JavaConfig项目提供的许多功能都成为核心Spring Framework的一部分。因此，您可以使用Java而不是XML文件在应用程序类外部定义bean。要使用这些新功能，请参阅`@Configuration`，`@ Label`，`@ Import`和`@DependsOn`注释。

Spring配置由容器必须管理的至少一个且通常不止一个bean定义组成。基于XML的配置元数据显示这些bean在顶级&lt;beans /&gt;元素中配置为&lt;bean /&gt;元素。Java配置通常在@Configuration类中使用@Bean注释方法。

