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

#### Default initialization and destroy methods----默认初始化和销毁方法

当您编写初始化和销毁不使用特定于Spring的`InitializingBean`和`DisposableBean`回调接口的方法回调时，通常会编写名称为`init（）`，`initialize（）`，`dispose（）`等的方法。理想情况下，此类生命周期回调方法的名称在项目中是标准化的，以便所有开发人员使用相同的方法名称并确保一致性。

您可以配置Spring容器以查找`look`命名初始化并销毁每个bean上的回调方法名称。这意味着，作为应用程序开发人员，您可以编写应用程序类并使用名为init（）的初始化回调，而无需为每个bean定义配置init-method =“init”属性。Spring IoC容器在创建bean时调用该方法（并且按照前面描述的标准生命周期回调契约）。此功能还强制执行初始化和销毁方法回调的一致命名约定。

假设您的初始化回调方法名为init（），而destroy回调方法名为destroy（）。 您的类将类似于以下示例中的类。

```
public class DefaultBlogService implements BlogService {

    private BlogDao blogDao;

    public void setBlogDao(BlogDao blogDao) {
        this.blogDao = blogDao;
    }

    // this is (unsurprisingly) the initialization callback method
    public void init() {
        if (this.blogDao == null) {
            throw new IllegalStateException("The [blogDao] property must be set.");
        }
    }
}
```

```
<beans default-init-method="init">

    <bean id="blogService" class="com.foo.DefaultBlogService">
        <property name="blogDao" ref="blogDao" />
    </bean>

</beans>
```

顶级`<beans />`元素属性上存在`default-init-method`属性会导致Spring IoC容器将bean上的`init`方法识别为初始化方法回调。在创建和组装bean时，如果bean类有这样的方法，就在适当的时候调用它。

您可以通过在顶级`<beans />`元素上使用`default-destroy-method`属性来类似地配置destroy方法回调（在XML中）。

如果现有bean类已经具有以约定方式命名的回调方法，则可以通过使用init-method和&lt;bean /&gt;的destroy-method属性指定（在XML中，）方法名称来覆盖缺省值。

Spring容器保证在为bean提供所有依赖项之后立即调用已配置的初始化回调。因此，在原始bean引用上调用初始化回调，这意味着AOP拦截器等尚未应用于bean。**首先完整地创建目标bean，然后应用AOP代理\(例如\)及其拦截器链**。如果目标bean和代理是分开定义的，那么您的代码甚至可以绕过代理与原始目标bean进行交互。因此，将拦截器应用到init方法将是不一致的，因为这样做将把目标bean的生命周期与其代理/拦截器结合起来，并且在代码直接与原始目标bean交互时留下奇怪的语义。

