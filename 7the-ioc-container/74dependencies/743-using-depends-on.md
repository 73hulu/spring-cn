如果bean是另一个bean的依赖项，通常意味着将一个bean设置为另一个bean的属性。通常，您可以使用基于XML的配置元数据中的`<ref />`元素[element](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-ref-element)来完成此操作。但是，**有时bean之间的依赖关系不那么直接**; 例如，需要触发类中的静态初始化程序，例如数据库驱动程序注册。在初始化使用此元素的bean之前，**depends-on属性可以显式强制初始化一个或多个bean**。以下示例使用depends-on属性表示对单个bean的依赖关系：

```
<bean id="beanOne" class="ExampleBean" depends-on="manager"/>
<bean id="manager" class="ManagerBean" />
```

要表示对多个bean的依赖关系，请提供bean名称列表作为depends-on属性的值，使用逗号，空格和分号作为有效分隔符：

```
<bean id="beanOne" class="ExampleBean" depends-on="manager,accountDao">
    <property name="manager" ref="manager" />
</bean>

<bean id="manager" class="ManagerBean" />
<bean id="accountDao" class="x.y.jdbc.JdbcAccountDao" />
```

> bean定义中的depends-on属性既可以指定初始化时间依赖关系，也可以指定仅限单例bean的相应销毁时间依赖关系。在给定的bean本身被销毁之前，首先销毁定义与给定bean的依赖关系的从属bean。 因此，依赖也可以控制关机顺序。



