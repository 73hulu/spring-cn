[Section 7.3.1, “Naming beans”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-beanname)Spring IoC容器管理一个或多个beans。这些bean是使用您提供给容器的配置元数据创建的，例如，以XML &lt;bean /&gt;定义的形式。

在容器本身内，这些bean定义表示为`BeanDefinition`对象，其中包含（以及其他信息）以下元数据：

* 包限定的类名：通常是正在定义的bean的实际实现类。
* Bean行为配置元素，说明bean在容器中的行为方式（范围，生命周期回调等）。
* 引用bean执行其工作所需的其他bean; 这些引用也称为协作者或依赖项。
* 要在新创建的对象中设置的其他配置设置，例如，在管理连接池的Bean中使用的连接数，或池的大小限制。

此元数据转换为组成每个bean定义的一组属性。

表7.1 bean的定义

| Property | Explained in…​\(解释在...\) |
| :--- | :--- |
| class | [Section 7.3.2, “Instantiating beans”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-class) |
| name | [Section 7.3.1, “Naming beans”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-beanname) |
| scope | [Section 7.5, “Bean scopes”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes) |
| constructor arguments | [Section 7.4.1, “Dependency Injection”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-collaborators) |
| properties | [Section 7.4.1, “Dependency Injection”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-collaborators) |
| autowiring mode | [Section 7.4.5, “Autowiring collaborators”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-autowire) |
| lazy-initialization mode | [Section 7.4.4, “Lazy-initialized beans”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-lazy-init) |
| initialization method | [the section called “Initialization callbacks”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-lifecycle-initializingbean) |
| destruction method | [the section called “Destruction callbacks”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-lifecycle-disposablebean) |



