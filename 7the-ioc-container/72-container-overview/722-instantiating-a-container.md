实例化Spring IoC容器非常简单。 提供给`ApplicationContext`构造函数的位置路径实际上是资源字符串，允许容器从各种外部资源（如本地文件系统，Java `CLASSPATH`等）加载配置元数据。

```
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

> 在了解Spring IoC容器之后，您可能想要了解有关Spring Resource抽象的更多信息，如[Chapter 8,Resources](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#resources)中所述，它提供了一种方便的机制，用于从URI语法中定义的位置读取InputStream。 特别是，资源路径用于构建应用程序上下文，如中所述[Section 8.7, “Application contexts and Resource paths”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#resources-app-ctx)。

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



