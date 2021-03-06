我们将看到的下一个扩展点是org.springframework.beans.factory.config.BeanFactoryPostProcessor。此接口的语义类似于BeanPostProcessor的语义，但有一个主要区别：BeanFactoryPostProcessor对bean配置元数据进行操作；也就是说，Spring IoC容器允许BeanFactoryPostProcessor读取配置元数据，并可能在容器实例化任何bean之前更改它但是除BeanFactoryPostProcessor之外。

您可以配置多个`BeanFactoryPostProcessors`，并且可以通过设置order属性来控制这些`BeanFactoryPostProcessors`的执行顺序。但是，如果BeanFactoryPostProcessor实现Ordered接口，则只能设置此属性。如果编写自己的`BeanFactoryPostProcessor`，则应考虑实现`Ordered`接口。 有关更多详细信息，请参阅`BeanFactoryPostProcessor`和`Ordered`接口的javadoc。

> 如果要更改实际的bean实例（即，从配置元数据创建的对象），则需要使用BeanPostProcessor（如上面第7.8.1节“使用BeanPostProcessor定制bean”中所述[Section 7.8.1, “Customizing beans using a BeanPostProcessor”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-extension-bpp)）。虽然技术上可以在BeanFactoryPostProcessor中使用bean实例（例如，使用BeanFactory.getBean（）），但这样做会导致过早的bean实例化，从而违反标准的容器生命周期。这可能会导致负面影响，例如绕过bean后处理。
>
> 此外，`BeanFactoryPostProcessors`的范围是每个容器。这仅在您使用容器层次结构时才相关。如果在一个容器中定义`BeanFactoryPostProcessor`，它将仅应用于该容器中的bean定义。`BeanFactoryPostProcessors`不会在另一个容器中对一个容器中的Bean定义进行后处理，即使两个容器都是同一层次结构的一部分。

在ApplicationContext中声明bean factory post-processor时，将自动执行，以便对定义容器的配置元数据应用更改。Spring包含许多预定义的bean factory post-processor，例如`PropertyOverrideConfigurer`和`PropertyPlaceholderConfigurer。`例如，也可以使用自定义BeanFactoryPostProcessor来注册自定义属性编辑器。

ApplicationContext自动检测部署到其中的任何实现BeanFactoryPostProcessor接口的bean。它在适当的时候使用这些bean作为bean factory post-processor。 您可以像处理任何其他bean一样部署这些后处理器bean。

> 与BeanPostProcessors一样，您通常不希望为延迟初始化配置BeanFactoryPostProcessors。如果没有其他bean引用Bean（Factory）PostProcessor，则该后处理器根本不会被实例化。因此，将其标记为延迟初始化将被忽略，即使您在元素的声明中将default-lazy-init属性设置为true,Bean\(Factory\)PostProcessor也将被立即实例化。

#### Example: the Class name substitution PropertyPlaceholderConfigurer

使用PropertyPlaceholderConfigurer将属性值从bean定义外部化到使用标准Java `Properties`格式的单独文件中。这样做可以使部署应用程序的人员自定义特定于环境的属性（如数据库URL和密码），而无需修改主XML定义文件或容器文件的复杂性或风险。

请考虑以下基于XML的配置元数据片段，其中定义了具有占位符值的DataSource。该示例显示了从外部属性文获取件配置的属性。在运行时，PropertyPlaceholderConfigurer应用于将替换DataSource的某些属性的元数据。要替换的值指定为$ {property-name}形式的占位符，该形式遵循Ant / log4j / JSP EL样式。

```
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="locations" value="classpath:com/foo/jdbc.properties"/>
</bean>

<bean id="dataSource" destroy-method="close"
        class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="${jdbc.driverClassName}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```

实际值来自标准Java Properties格式的另一个文件：

```
jdbc.driverClassName=org.hsqldb.jdbcDriver
jdbc.url=jdbc:hsqldb:hsql://production:9002
jdbc.username=sa
jdbc.password=root
```

因此，字符串$ {jdbc.username}在运行时将替换为值'sa'，这同样适用于与属性文件中的键匹配的其他占位符值。PropertyPlaceholderConfigurer检查bean定义的大多数属性和属性中的占位符。 此外，可以自定义占位符前缀和后缀。

使用Spring 2.5中引入的上下文命名空间，可以使用专用配置元素配置属性占位符。可以在location属性中提供一个或多个位置作为逗号分隔列表。

```
<context:property-placeholder location="classpath:com/foo/jdbc.properties"/>
```

PropertyPlaceholderConfigurer不仅在您指定的属性文件中查找属性。默认情况下，如果它无法在指定的属性文件中找到属性，它还会检查Java System属性。您可以通过使用以下三个整数值之一设置configurer的systemPropertiesMode属性来自定义此行为：

* never \(0\)：从不检查系统属性
* fallback \(1\):：如果在指定的属性文件中无法解析，请检查系统属性。 这是默认值。
* override \(2\)：在尝试指定的属性文件之前，首先检查系统属性。 这允许系统属性覆盖任何其他属性源。

有关更多信息，请参阅PropertyPlaceholderConfigurer javadocs。

> 您可以使用PropertyPlaceholderConfigurer替换类名，这在您必须在运行时选择特定实现类时有时很有用。 例如：
>
> ```
> <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
>     <property name="locations">
>         <value>classpath:com/foo/strategy.properties</value>
>     </property>
>     <property name="properties">
>         <value>custom.strategy.class=com.foo.DefaultStrategy</value>
>     </property>
> </bean>
>
> <bean id="serviceStrategy" class="${custom.strategy.class}"/>
> ```
>
> 如果在运行时不能将类解析为有效的类，则bean的解析将在将要创建时失败，这是在非lazy-init bean的ApplicationContext的预实例化阶段。

#### Example: the PropertyOverrideConfigurer

PropertyOverrideConfigurer是另一个bean factory post-processor，类似于PropertyPlaceholderConfigurer，但与后者不同，原始定义可以具有默认值，或者根本不具有bean属性的值。如果重写的Properties文件没有某个bean属性的条目，则使用默认的上下文定义。

请注意，bean定义并不知道被覆盖，因此从XML定义文件中不能立即看出正在使用覆盖配置程序。如果多个PropertyOverrideConfigurer实例为同一个bean属性定义了不同的值，则由于覆盖机制，最后一个实例会获胜。

属性文件配置行采用以下格式：

```
beanName.property=value
```

例如：

```
dataSource.driverClassName=com.mysql.jdbc.Driver
dataSource.url=jdbc:mysql:mydb
```

此示例文件可以与包含名为dataSource的bean的容器定义一起使用，该bean具有驱动程序和url属性。

还支持复合属性名，只要路径的每个组件\(最终被覆盖的属性除外\)已经是非空的\(假定由构造函数初始化\)。在这个例子中……

```
foo.fred.bob.sammy=123
```

foo bean的fred属性的bob属性的sammy属性设置为标量值123。

> 指定的覆盖值始终是文字值; 它们不会被翻译成bean引用。当XML bean定义中的原始值指定bean引用时，也适用此约定。

使用Spring 2.5中引入的上下文命名空间，可以使用专用配置元素配置属性覆盖：

```
<context:property-override location="classpath:override.properties"/>
```



