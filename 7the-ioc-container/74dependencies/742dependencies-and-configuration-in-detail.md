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

#### Collections----集合

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

Spring容器还支持集合的合并。应用程序开发人员可以定义父样式的&lt;list /&gt;，&lt;map /&gt;，&lt;set /&gt;或&lt;props /&gt;元素，并具有子样式&lt;list /&gt;，&lt;map /&gt;，&lt;set /&gt;或 &lt;props /&gt;元素继承并覆盖父集合中的值。也就是说，子集合的值是合并父集合和子集合的元素的结果，子集合的元素覆盖父集合中指定的值。

关于合并的这一部分讨论了父子bean机制。 不熟悉父母和子bean定义的读者可能希望在继续之前阅读相关部分[_relevant section_](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-child-bean-definitions)。

以下示例演示了集合合并：

```
<beans>
    <bean id="parent" abstract="true" class="example.ComplexObject">
        <property name="adminEmails">
            <props>
                <prop key="administrator">administrator@example.com</prop>
                <prop key="support">support@example.com</prop>
            </props>
        </property>
    </bean>
    <bean id="child" parent="parent">
        <property name="adminEmails">
            <!-- the merge is specified on the child collection definition -->
            <props merge="true">
                <prop key="sales">sales@example.com</prop>
                <prop key="support">support@example.co.uk</prop>
            </props>
        </property>
    </bean>
<beans>
```

请注意在子bean定义的`adminEmails`属性的`<props />`元素上使用`merge = true`属性。当容器解析并实例化子bean时，生成的实例有一个`adminEmails`属性集合，其中包含子项的adminEmails集合与父项的adminEmails集合的合并结果。

```
administrator=administrator@example.com
sales=sales@example.com
support=support@example.co.uk
```

子属性集合的值集继承父&lt;props /&gt;的所有属性元素，子值的支持值覆盖父集合中的值。

此合并行为同样适用于`<list />`，`<map />`和`<set />`集合类型。在`<list />`元素的特定情况下，保持与List集合类型相关联的语义，即有序的值集合的概念; 父级的值位于所有子级列表的值之前。对于`Map`，`Set`和`Properties`集合类型，不存在排序。因此，对于作为容器内部使用的关联`Map`，`Set`和`Properties`实现类型的基础的集合类型，没有排序语义。

##### Limitations of collection merging---集合合并的局限性

您不能合并不同的集合类型（例如`Map`和`List`），如果您尝试这样做，则会抛出相应的`Exception`。必须在较低的继承子定义上指定merge属性; 在父集合定义上指定merge属性是多余的，不会导致所需的合并。

##### Strongly-typed collection----强类型集合

通过在Java 5中引入泛型类型，您可以使用强类型集合。也就是说，可以声明Collection类型，使其只能包含String元素（例如）。如果您使用Spring依赖注入一个强类型的Collection到bean中，您可以利用Spring的类型转换支持，以便强类型Collection实例的元素在添加到之前转换为适当的类型 集合。

```
public class Foo {

    private Map<String, Float> accounts;

    public void setAccounts(Map<String, Float> accounts) {
        this.accounts = accounts;
    }
}
```

```
<beans>
    <bean id="foo" class="x.y.Foo">
        <property name="accounts">
            <map>
                <entry key="one" value="9.99"/>
                <entry key="two" value="2.75"/>
                <entry key="six" value="3.99"/>
            </map>
        </property>
    </bean>
</beans>
```

当foo bean的`accounts`属性准备好进行注入时，可以通过反射获得有关强类型`Map <String，Float>`的元素类型的泛型信息。因此，Spring的类型转换基础结构将各种值元素识别为`Float`类型，并将字符串值`9.99`,`2.75`和`3.99`转换为实际的`Float`类型。

#### Null and empty string values----空值和空字符串值

pring将属性等的空参数视为空字符串。 以下基于XML的配置元数据片段将email属性设置为空String值（“”）。

```
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```

前面的示例等效于以下Java代码：

```
exampleBean.setEmail("");
```

`<null />`元素处理空值。 例如：

```
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```

以上配置等同于以下Java代码：

```
exampleBean.setEmail(null);
```

#### XML shortcut with the p-namespace----带有p-namespace的XML快捷方式

p-namespace使您可以使用bean元素的属性而不是嵌套的&lt;property /&gt;元素来描述属性值或协作bean。

Spring支持具有命名空间的可扩展配置格式[with namespaces](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#xsd-configuration)，这些命名空间基于XML Schema定义。 本章中讨论的bean配置格式在XML Schema文档中定义。但是，`p-namespace未在XSD文件中定义，仅存在于Spring的核心中`。

以下示例显示了两个解析为相同结果的XML片段：第一个使用标准XML格式，第二个使用p-namespace。

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="classic" class="com.example.ExampleBean">
        <property name="email" value="foo@bar.com"/>
    </bean>

    <bean name="p-namespace" class="com.example.ExampleBean"
        p:email="foo@bar.com"/>
</beans>
```

该示例显示了bean定义中名为email的p-namespace中的属性。这告诉Spring包含一个属性声明。如前所述，p-namespace没有架构定义，因此您可以将属性的名称设置为property属性名称。

下一个示例包括另外两个bean定义，它们都引用了另一个bean：

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="john-classic" class="com.example.Person">
        <property name="name" value="John Doe"/>
        <property name="spouse" ref="jane"/>
    </bean>

    <bean name="john-modern"
        class="com.example.Person"
        p:name="John Doe"
        p:spouse-ref="jane"/>

    <bean name="jane" class="com.example.Person">
        <property name="name" value="Jane Doe"/>
    </bean>
</beans>
```

如您所见，此示例不仅包含使用p-namespace的属性值，还使用特殊格式来声明属性引用。第一个bean定义使用&lt;property name =“spouse”ref =“jane”/&gt;来创建从bean john到bean jane的引用，而第二个bean定义使用p:spouse-ref =“jane”作为要执行的属性 完全相同的事情。在这种情况下，spouse是属性名称，而-ref部分表示这不是直接值，而是对另一个bean的引用。

> p-namespace不如标准XML格式灵活。例如，声明属性引用的格式与以Ref结尾的属性冲突，而标准XML格式则不然。我们建议您仔细选择您的方法并将其传达给您的团队成员，以避免生成同时使用所有三种方法的XML文档。

#### XML shortcut with the c-namespace----带有c-namespace的XML快捷方式

类似于[the section called “XML shortcut with the p-namespace”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-p-namespace)，在Spring 3.1中新引入的c-namespace允许使用内联属性来配置构造函数参数，而不是嵌套的constructor-arg元素。

让我们回顾一下使用c：namespace命名的“基于构造函数的依赖注入”[the section called “Constructor-based dependency injection”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-constructor-injection)一节中的示例：

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bar" class="x.y.Bar"/>
    <bean id="baz" class="x.y.Baz"/>

    <!-- traditional declaration -->
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
        <constructor-arg value="foo@bar.com"/>
    </bean>

    <!-- c-namespace declaration -->
    <bean id="foo" class="x.y.Foo" c:bar-ref="bar" c:baz-ref="baz" c:email="foo@bar.com"/>

</beans>
```

c:namespace使用与p：one（用于bean引用的trailing -ref）相同的约定，用于按名称设置构造函数参数。 同样，它需要声明，即使它没有在XSD架构中定义（但它存在于Spring核心内）。

对于构造函数参数名称不可用的罕见情况（通常如果字节码是在没有调试信息的情况下编译的话），可以使用回退到参数索引：

```
<!-- c-namespace index declaration -->
<bean id="foo" class="x.y.Foo" c:_0-ref="bar" c:_1-ref="baz"/>
```

> 由于XML语法，索引表示法要求存在前导\_，因为XML属性名称不能以数字开头（即使某些IDE允许它）。

在实践中，构造函数解析机制[mechanism](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-ctor-arguments-resolution)在匹配参数方面非常有效，因此除非确实需要，否则我们建议在整个配置中使用名称表示法。

#### Compound property names----复合属性名称

只要除最终属性名称之外的路径的所有组件都不为null，您可以在设置bean属性时使用复合或嵌套属性名称。

请考虑以下bean定义。

```
<bean id="foo" class="foo.Bar">
    <property name="fred.bob.sammy" value="123" />
</bean>
```

foo bean有一个fred属性，它有一个bob属性，它有一个sammy属性，最后的sammy属性被设置为值123。为了使其工作，在构造bean之后，foo的fred属性和fred的bob属性不能为null，否则抛出NullPointerException。

