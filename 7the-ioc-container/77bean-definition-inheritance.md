bean定义可以包含许多配置信息，包括构造函数参数，属性值和特定于容器的信息，例如初始化方法，静态工厂方法名称等。子bean定义从父定义继承配置数据。子定义可以根据需要覆盖某些值或添加其他值。使用父bean和子bean定义可以节省大量的输入。 实际上，这是一种模板形式。

如果以编程方式使用ApplicationContext接口，则子bean定义由ChildBeanDefinition类表示。大多数用户不在此级别上使用它们，而是以类似ClassPathXmlApplicationContext的方式声明性地配置bean定义。使用基于XML的配置元数据时，可以使用parent属性指定子bean定义，并将父bean指定为此属性的值。

```
<bean id="inheritedTestBean" abstract="true"
        class="org.springframework.beans.TestBean">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithDifferentClass"
        class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBean" init-method="initialize">
    <property name="name" value="override"/>
    <!-- the age property value of 1 will be inherited from parent -->
</bean>
```

如果没有指定bean，子bean定义使用父定义的bean类，但也可以覆盖它。在后一种情况下，子bean类必须与父类兼容，也就是说，它必须接受父类的属性值。

子bean定义从父级继承范围，构造函数参数值，属性值和方法覆盖，并提供添加新值的选项。您指定的任何范围，初始化方法，销毁方法和/或静态工厂方法设置都将覆盖相应的父设置。

其余的设置总是取自子定义:依赖、自动连接模式、依赖检查、单例、惰性init。

前面的示例通过使用abstract属性将父bean定义显式标记为abstract。如果父定义未指定类，则需要将父bean定义显式标记为abstract，如下所示：

```
<bean id="inheritedTestBeanWithoutClass" abstract="true">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithClass" class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBeanWithoutClass" init-method="initialize">
    <property name="name" value="override"/>
    <!-- age will inherit the value of 1 from the parent bean definition-->
</bean>
```



