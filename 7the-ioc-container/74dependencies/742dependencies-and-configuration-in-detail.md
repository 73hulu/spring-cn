如前一节所述，可以将bean属性和构造函数参数定义为对其他托管bean\(协作者\)的引用，或内联定义的值。Spring的基于XML的配置元数据为此目的支持其`<property />`和`<constructor-arg />`元素中的子元素类型。

#### Straight values \(primitives, Strings, and so on\)----直接值（基元，字符串等）

`<property />`元素的value属性将属性或构造函数参数指定为人可读的字符串表示形式。Spring的[conversion service](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#core-convert-ConversionService-API)用于将这些值从`String`转换为属性或参数的实际类型。

```
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <!-- results in a setDriverClassName(String) call -->
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="masterkaoli"/>
</bean>
```

以下示例使用[p-namespace](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-p-namespace)进行更简洁的XML配置。

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close"
        p:driverClassName="com.mysql.jdbc.Driver"
        p:url="jdbc:mysql://localhost:3306/mydb"
        p:username="root"
        p:password="masterkaoli"/>

</beans>
```

前面的XML更简洁；但是，在运行时而不是设计时发现拼写错误，除非您在创建bean定义时使用支持自动属性完成的IntelliJ IDEA或Spring Tool Suite（STS）等IDE。强烈建议使用此类IDE帮助。

您还可以将`java.util.Properties`实例配置为：

```
<bean id="mappings"
    class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">

    <!-- typed as a java.util.Properties -->
    <property name="properties">
        <value>
            jdbc.driver.className=com.mysql.jdbc.Driver
            jdbc.url=jdbc:mysql://localhost:3306/mydb
        </value>
    </property>
</bean>
```

Spring容器通过使用JavaBeans `PropertyEditor`机制将`<value />`元素内的文本转换为`java.util.Properties`实例。这是一个很好的快捷方式，并且是Spring团队支持在`value`属性样式上使用嵌套`<value />`元素的少数几个地方之一。

##### The idref element----idref元素

`idref`元素只是一种防错方法，可以将容器中另一个bean的**id（字符串值 - 而不是引用）**传递给`<constructor-arg />`或`<property />`元素。

```
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean"/>
    </property>
</bean>
```

上面的bean定义代码段与以下代码段完全等效（在运行时）：

```
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
    <property name="targetName" value="theTargetBean"/>
</bean>
```

第一种形式优于第二种形式，因为使用`idref`标签允许容器在部署时验证引用的命名bean实际存在。在第二个变体中，不对传递给客户端bean的`targetName`属性的值执行验证。只有当`client`bean实际实例化时，才会发现输入错误\(最有可能导致致命的结果\)。如果`client`bean是原型bean，则只能在部署容器后很长时间才能发现此错误和产生的异常。

> 4.0 beans xsd不再支持idref元素的local属性，因为它不再提供常规bean引用的值。在升级到4.0模式时，只需将现有的idref本地引用更改为idref bean。

`<idref />`元素带来值的一个常见位置（至少在Spring 2.0之前的版本中）是在`ProxyFactoryBean`bean定义中的AOP拦截器的配置中。 指定拦截器名称时使用`<idref />`元素可以防止拼写错误的拦截器ID。

