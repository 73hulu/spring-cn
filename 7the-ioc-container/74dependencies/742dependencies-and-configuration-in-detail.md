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

#### References to other beans \(collaborators\)----引用其他bean（协作者）

`ref`元素是`<constructor-arg />`或`<property />`定义元素中的最后一个元素。在这里，您可以将bean的指定属性的值设置为对容器管理的另一个bean（协作者）的引用。引用的bean是bean的依赖项，其属性将被设置，并且在设置属性之前根据需要按需初始化。（如果协作者是单例bean，它可能已被容器初始化。）所有引用最终都是对另一个对象的引用。作用域和验证取决于是否通过bean，local或parent属性指定其他对象的id / name。

通过`<ref />`标记的`bean`属性指定目标bean是最常用的形式，并允许在同一容器或父容器中创建对任何bean的引用，而不管它是否在同一XML文件中。`bean`属性的值可以与目标bean的id属性相同，也可以作为目标bean的name属性中的值之一。

```
<ref bean="someBean"/>
```

通过`parent`属性指定目标bean会创建对当前容器的父容器中的bean的引用。parent属性的值可以与目标bean的id属性相同，也可以与目标bean的name属性中的某个值相同，并且目标bean必须位于当前bean的父容器中。您主要在拥有容器层次结构并且希望将现有bean包装在父容器中并使用与父bean具有相同名称的代理时使用此Bean引用变体。

```
<!-- in the parent context -->
<bean id="accountService" class="com.foo.SimpleAccountService">
    <!-- insert dependencies as required as here -->
</bean>
```

```
<!-- in the child (descendant) context -->
<bean id="accountService" <!-- bean name is the same as the parent bean -->
    class="org.springframework.aop.framework.ProxyFactoryBean">
    <property name="target">
        <ref parent="accountService"/> <!-- notice how we refer to the parent bean -->
    </property>
    <!-- insert other configuration and dependencies as required here -->
</bean>
```

> 4.0 bean xsd不再支持ref元素的local属性，因为它不再提供常规bean引用的值。升级到4.0架构时，只需将现有的ref local引用更改为ref bean。

#### Inner beans----内部类

`<property />`或`<constructor-arg />`元素中的`<bean />`元素定义了一个所谓的内部bean。

```
<bean id="outer" class="...">
    <!-- instead of using a reference to a target bean, simply define the target bean inline -->
    <property name="target">
        <bean class="com.example.Person"> <!-- this is the inner bean -->
            <property name="name" value="Fiona Apple"/>
            <property name="age" value="25"/>
        </bean>
    </property>
</bean>
```

内部bean定义不需要定义的id或名称; 如果指定，则容器不使用这样的值作为标识符。容器还会在创建时忽略scope作用域标志：内部bean总是匿名的，它们总是使用外部bean创建。无法将内部bean注入协作bean，而不是将其注入封闭bean或独立访问它们。

作为极端情况，可以从自定义范围接收销毁回调，例如， 对于包含在单例bean中的请求范围的内部bean：内部bean实例的创建将绑定到其包含的bean，但是销毁回调允许它参与请求范围的生命周期。 这不是常见的情况; 内部bean通常只是共享其包含bean的范围。

####  Collections----集合

在`<list />`，`<set />`，`<map />`和`<props />`元素中，分别设置Java `Collection`类型`List`，`Set`，`Map`和`Properties`的属性和参数。

```
<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- results in a setAdminEmails(java.util.Properties) call -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
    <!-- results in a setSomeList(java.util.List) call -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- results in a setSomeMap(java.util.Map) call -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key ="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- results in a setSomeSet(java.util.Set) call -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
</bean>
```

map键或值，或set值的值也可以是以下元素之一:

> ```
> bean | ref | idref | list | set | map | props | value | null
> ```

##### Collection merging----集合合并



