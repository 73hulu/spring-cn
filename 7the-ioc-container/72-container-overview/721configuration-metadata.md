如上图所示，Spring IoC容器使用一种配置元数据形式; 此配置元数据表示您作为应用程序开发人员如何告诉Spring容器在应用程序中实例化，配置和组装对象。

传统上，配置元数据以简单直观的XML格式提供，本章大部分内容用于传达Spring IoC容器的关键概念和功能。

> 基于XML的元数据不是唯一允许的配置元数据形式。Spring IoC容器本身完全与实际编写此配置元数据的格式分离。目前，许多开发人员为其Spring应用程序选择基于Java的配置[Java-based configuration](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java)。

有关在Spring容器中使用其他形式的元数据的信息，请参阅：

* [Annotation-based configuration](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-annotation-config)：Spring 2.5引入了对基于注释的配置元数据的支持。

* [Java-based configuration](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java)：从Spring 3.0开始，Spring JavaConfig项目提供的许多功能都成为核心Spring Framework的一部分。因此，您可以使用Java而不是XML文件在应用程序类外部定义bean。要使用这些新功能，请参阅`@Configuration`，`@ Label`，`@ Import`和`@DependsOn`注释。

Spring配置由容器必须管理的至少一个且通常不止一个bean定义组成。基于XML的配置元数据显示这些bean在顶级&lt;beans /&gt;元素中配置为&lt;bean /&gt;元素。Java配置通常在@Configuration类中使用@Bean注释方法。

这些bean定义对应于构成应用程序的实际对象。通常，您定义服务层对象，数据访问对象（DAO），表示对象（如Struts Action实例），基础结构对象（如Hibernate SessionFactories，JMS队列等）。通常，不会在容器中配置细粒度域对象，因为创建和加载域对象通常由DAO和业务逻辑负责。但是，您可以**使用Spring与AspectJ的集成来配置在IoC容器控制之外创建的对象**。请参见[Using AspectJ to dependency-inject domain objects with Spring](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#aop-atconfigurable)

.以下示例显示了基于XML的配置元数据的基本结构：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

`id`属性是一个字符串，用于标识单个bean定义。`class`属性定义bean的类型并使用完全限定的classname。id属性的值指的是协作对象。本例中未显示用于引用协作对象的XML；有关更多信息，请参阅[Dependencies](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-dependencies)。



