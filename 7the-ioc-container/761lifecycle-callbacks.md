要与容器的bean生命周期管理进行交互，可以实现Spring InitializingBean和DisposableBean接口。容器为前者调用afterPropertiesSet（），为后者调用destroy（）以允许bean在初始化和销毁bean时执行某些操作。

> JSR-250`@PostConstruct`和`@PreDestroy`注释通常被认为是在现代Spring应用程序中接收生命周期回调的最佳实践。使用这些注释意味着您的bean不会耦合到Spring特定的接口。更详细信息请查看[Section 7.9.8, “@PostConstruct and @PreDestroy”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-postconstruct-and-predestroy-annotations)。
>
> 如果您不想使用JSR-250注释但仍希望删除耦合，请考虑使用init-method和destroy-method对象定义元数据。

在内部，Spring Framework使用`BeanPostProcessor`实现来处理它可以找到的任何回调接口并调用适当的方法。如果您需要自定义特性或Spring不提供的其他生命周期行为，您可以自己实现BeanPostProcessor。 欲获得更多信息，请查看[Section 7.8, “Container Extension Points”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-extension)。

除了初始化和销毁回调之外，Spring管理的对象还可以实现Lifecycle接口，以便这些对象可以参与由容器自身生命周期驱动的启动和关闭过程。

本节描述了生命周期回调接口。

#### Initialization callbacks----初始化回调

`org.springframework.beans.factory.InitializingBean`接口允许bean在容器设置bean的所有必要属性后执行初始化工作。InitializingBean接口指定一个方法：

```
void afterPropertiesSet() throws Exception;
```

建议您不要使用InitializingBean接口，因为它会不必要地将代码耦合到Spring。或者，使用`@PostConstruct`注释或指定POJO初始化方法。对于基于XML的配置元数据，可以使用init-method属性指定具有void无参数签名的方法的名称。使用Java配置，您可以使用@Bean的initMethod属性，请查看[the section called “Receiving lifecycle callbacks”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java-lifecycle-callbacks)。例如，以下内容：

```
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>
```

```
public class ExampleBean {

    public void init() {
        // do some initialization work
    }
}
```

与下面的完全一样

```
<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
```

```
public class AnotherExampleBean implements InitializingBean {

    public void afterPropertiesSet() {
        // do some initialization work
    }
}
```

但不会将代码耦合到Spring。

#### Destruction callbacks----销毁回调

实现`org.springframework.beans.factory.DisposableBean`接口允许bean在包含它的容器被销毁时获得回调。

DisposableBean接口指定一个方法：

```
void destroy() throws Exception;
```

建议您不要使用DisposableBean回调接口，因为它会不必要地将代码耦合到Spring。或者，使用@PreDestro注释或指定bean定义支持的泛型方法。使用基于XML的配置元数据，可以在&lt;bean /&gt;上使用destroy-method属性。使用Java配置，您可以使用@Bean的destroyMethod属性，请查看[the section called “Receiving lifecycle callbacks”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java-lifecycle-callbacks)。例如，以下内容：

```
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
```

```
public class ExampleBean {

    public void cleanup() {
        // do some destruction work (like releasing pooled connections)
    }
}
```

与下面的完全一样

```
<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
```

```
public class AnotherExampleBean implements DisposableBean {

    public void destroy() {
        // do some destruction work (like releasing pooled connections)
    }
}
```

但不会将代码耦合到Spring。

> 可以为&lt;bean&gt;元素的destroy-method属性分配一个特殊的（作为推断）值，该值指示Spring自动检测特定bean类（任何实现java.lang.AutoCloseable或java.io.Closeable的类）的公共关闭或关闭方法）。也可以在&lt;beans&gt;元素的default-destroy-method属性上设置此特殊（作为推断）值，以将此行为应用于整组bean（请查看[the section called “Default initialization and destroy methods”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-lifecycle-default-init-destroy-methods)）。请注意，这是Java配置的默认行为。



