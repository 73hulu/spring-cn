实例化Spring IoC容器非常简单。 提供给`ApplicationContext`构造函数的位置路径实际上是资源字符串，允许容器从各种外部资源（如本地文件系统，Java `CLASSPATH`等）加载配置元数据。

```
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

> 在了解Spring IoC容器之后，您可能想要了解有关Spring Resource抽象的更多信息，如[Chapter 8,Resources](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#resources)中所述，它提供了一种方便的机制，用于从URI语法中定义的位置读取InputStream。 特别是，资源路径用于构建应用程序上下文，如中所述[Section 8.7, “Application contexts and Resource paths”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#resources-app-ctx)。

以下示例显示了服务层对象（services.xml）配置文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```

以下示例显示了数据访问对象daos.xml文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao"
        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for data access objects go here -->

</beans>
```

在前面的示例中，服务层由`PetStoreServiceImpl`类和两个`JpaAccountDao`和`JpaItemDao`类型的数据访问对象组成（基于JPA对象/关系映射标准）。`property name`元素引用JavaBean属性的名称，`ref`元素引用另一个bean定义的名称。`id`和`ref`元素之间的这种联系表达了协作对象之间的依赖关系。 有关配置对象的依赖项的详细信息，请参阅[Dependencies](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-dependencies)。

#### Composing XML-based configuration metadata

让bean定义跨越多个XML文件会很有用。 通常，每个单独的XML配置文件都代表架构中的逻辑层或模块。您可以使用应用程序上下文构造函数从所有这些XML片段加载bean定义。此构造函数采用多个Resource位置，如上一节中所示。或者，使用一个或多个`<import />`元素来从另一个或多个文件加载bean定义。 例如：

```
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```

在前面的示例中，外部bean定义从三个文件加载：`services.xml`，`messageSource.xml`和`themeSource.xml`。所有位置路径都与执行导入的定义文件相关，因此`services.xml`必须与执行导入的文件位于相同的目录或类路径位置，而`messageSource.xml`和`themeSource.xml`必须位于该位置下的资源位置 导入文件。正如您所看到的，忽略了一个前导斜杠，但考虑到这些路径是相对的，最好不要使用斜杠。 根据Spring Schema，要导入的文件的内容（包括顶级&lt;beans /&gt;元素）必须是有效的XML bean定义。

> 可以（但不建议）使用相对“../”路径引用父目录中的文件。这样做会对当前应用程序之外的文件创建依赖关系。特别是，不建议将此引用用于“classpath：”URL（例如，“classpath：../ services.xml”），其中运行时解析过程选择“最近的”类路径根，然后查看其父目录。类路径配置更改可能导致选择不同的，不正确的目录。
>
> 您始终可以使用完全限定的资源位置而不是相对路径：例如，“file：C：/config/services.xml”或“classpath：/config/services.xml”。但是，请注意您将应用程序的配置与特定的绝对位置耦合。通常最好为这样的绝对位置保持间接，例如，通过在运行时针对JVM系统属性解析的“$ {...}”占位符。

import指令是beans命名空间本身提供的功能。 除了普通bean定义之外的其他配置功能在Spring提供的一系列XML命名空间中可用，例如： “context”和“util”命名空间。

#### The Groovy Bean Definition DSL

作为外部化配置元数据的另一个示例，bean定义也可以在Spring的Groovy Bean定义DSL中表示，如Grails框架中所知。 通常，此类配置将存在“.groovy”文件中，其结构如下：

```
beans {
    dataSource(BasicDataSource) {
        driverClassName = "org.hsqldb.jdbcDriver"
        url = "jdbc:hsqldb:mem:grailsDB"
        username = "sa"
        password = ""
        settings = [mynew:"setting"]
    }
    sessionFactory(SessionFactory) {
        dataSource = dataSource
    }
    myService(MyService) {
        nestedBean = { AnotherBean bean ->
            dataSource = dataSource
        }
    }
}
```



