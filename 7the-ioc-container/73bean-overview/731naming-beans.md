每个bean都有一个或多个标识符。这些标识符在托管bean的容器中必须是唯一的。bean通常只有一个标识符，但如果它需要多个标识符，则额外的标识符可以被视为别名。

在基于XML的配置元数据中，您使用id或name属性来指定bean标识符。id属性允许您指定一个id。通常，这些名称是字母数字（'myBean'，'fooService'等），但也可能包含特殊字符。如果要向bean引入其他别名，还可以在name属性中指定它们，用逗号（，），分号（;）或空格分隔。作为历史记录，在Spring 3.1之前的版本中，id属性被定义为xsd:ID类型，它约束了可能的字符。从3.1开始，它被定义为xsd:string类型。 请注意，bean ID唯一性仍由容器强制执行，但不再由XML解析器强制执行。

您不需要为bean提供名称或ID。如果没有显式提供名称或标识，则容器会为该bean生成唯一的名称。但是，如果要通过名称引用该bean，通过使用ref元素或[Service Locator](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-servicelocator)方式查找，则必须提供名称。不提供名称的动机与使用[inner beans](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-inner-beans)和[autowiring collaborators](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-autowire)有关。

```
Bean命名约定

惯例是在命名bean时使用标准Java约定作为实例字段名称。也就是说，bean名称以小写字母开头，从那时起就是驼峰式的。这些名称的示例是（没有引号）'accountManager'，'accountService'，'userDao'，'loginController'等等。

命名bean始终使您的配置更易于阅读和理解，如果您使用的是Spring AOP，那么在将建议应用于与名称相关的一组bean时，它会有很大帮助。
```

> 使用类路径中的组件扫描，Spring会根据上述规则为未命名的组件生成bean名称：实质上，采用简单的类名并将其初始字符转换为小写。但是，在（不常见的）特殊情况下，当有多个字符并且第一个和第二个字符都是大写字母时，原始外壳将被保留。这些规则与java.beans.Introspector.decapitalize（Spring在此处使用）定义的规则相同。

#### Aliasing a bean outside the bean definition（在bean定义之外别名bean）

在bean定义本身中，您可以为bean提供多个名称，方法是使用id属性指定的最多一个名称和name属性中的任意数量的其他名称。这些名称可以是同一个bean的等效别名，并且在某些情况下很有用，例如允许应用程序中的每个组件通过使用特定于该组件本身的bean名称来引用公共依赖项。

但是，指定实际定义bean的所有别名并不总是足够的。 有时需要为其他地方定义的bean引入别名。在大型系统中通常就是这种情况，其中配置在每个子系统之间分配，每个子系统具有其自己的一组对象定义。在基于XML的配置元数据中，您可以使用&lt;alias /&gt;元素来完成此任务。

```
<alias name="fromName" alias="toName"/>
```





