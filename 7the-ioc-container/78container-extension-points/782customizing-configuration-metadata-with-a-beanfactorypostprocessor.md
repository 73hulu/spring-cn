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





