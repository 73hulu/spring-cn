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

#### Combining lifecycle mechanisms----结合生命周期机制

从Spring 2.5开始，您有三个控制bean生命周期行为的选项：InitializingBean和DisposableBean回调接口; 自定义init（）和destroy（）方法; 以及@PostConstruct和@PreDestroy注释。您可以组合这些机制来控制给定的bean。

> 如果为bean配置了多个生命周期机制，并且每个机制都配置了不同的方法名称，则每个配置的方法都按照下面列出的顺序执行。但是，如果为配置了相同的方法名称（例如，init（） - 对于多个这些生命周期机制，该方法将执行一次，如上一节中所述。

为同一个bean配置的多个生命周期机制，具有不同的初始化方法，如下所示：

* 使用@PostConstruct注释的方法
* afterPropertiesSet（）由InitializingBean回调接口定义
* 自定义配置的init（）方法

Destroy方法以相同的顺序调用：

* 用@PreDestroy注释的方法
* destroy（），由DisposableBean回调接口定义
* 自定义配置的destroy（）方法

#### Startup and shutdown callbacks----启动和关闭回调

`Lifecycle`接口为任何具有自己生命周期要求的对象定义基本方法（例如，启动和停止某些后台进程）：

```
public interface Lifecycle {

    void start();

    void stop();

    boolean isRunning();
}
```

任何Spring管理的对象都可以实现该接口。然后，当`ApplicationContext`本身接收到启动和停止信号时，例如， 对于运行时的停止/重新启动方案，它会将这些调用级联到该上下文中定义的所有生命周期`Lifecycle说`实现。它通过委托给`LifecycleProcessor`来实现：

```
public interface LifecycleProcessor extends Lifecycle {

    void onRefresh();

    void onClose();
}
```

请注意，`LifecycleProcessor`本身是`Lifecycle`接口的扩展。它还添加了另外两种方法来响应刷新和关闭的上下文。

> 请注意，常规`org.springframework.context.Lifecycle`接口只是显式启动/停止通知的简单合约，并不意味着在上下文刷新时自动启动。考虑实现`org.springframework.context.SmartLifecycle`，以便对特定bean的自动启动进行细粒度控制（包括启动阶段）。另请注意，在销毁之前不能保证停止通知：在常规关闭时，所有生命周期 bean将在传播一般销毁回调之前首先收到停止通知; 但是，在上下文生命周期中的热刷新或中止刷新尝试时，只会调用destroy方法。

启动和关闭调用的顺序非常重要。如果任何两个对象之间存在“依赖”关系，则依赖方将在其依赖之后启动，并且它将在其依赖之前停止。但是，有时直接依赖性是未知的。您可能只知道某种类型的对象应该在另一种类型的对象之前开始。在这些情况下，SmartLifecycle接口定义了另一个选项，即在其超级接口Phased上定义的getPhase（）方法。

```
public interface Phased {

    int getPhase();
}
```

```
public interface SmartLifecycle extends Lifecycle, Phased {

    boolean isAutoStartup();

    void stop(Runnable callback);
}
```

启动时，相位（phase）最低的对象先启动，停止时顺序相反。因此，实现SmartLifecycle并且其getPhase（）方法返回Integer.MIN\_VALUE的对象将是第一个开始和最后一个停止的对象。在频谱的另一端，相位值Integer.MAX\_VALUE将指示对象应该最后启动并首先停止（可能是因为它依赖于正在运行的其他进程）。在考虑相位值时，同样重要的是要知道任何未实现SmartLifecycle的“正常”Lifecycle对象的默认阶段都是0.因此，任何负相位值都表示对象应该在这些标准组件之前启动（和 在他们之后停止），反之亦然，任何正相位值。

如您所见，SmartLifecycle定义的stop方法接受回调。任何实现必须在该实现的关闭过程完成后调用该回调的run（）方法。这样就可以在必要时启用异步关闭，因为LifecycleProcessor接口的默认实现DefaultLifecycleProcessor将等待每个阶段内对象组的超时值来调用该回调。默认的每阶段超时为30秒。您可以通过在上下文中定义名为“lifecycleProcessor”的bean来覆盖缺省生命周期处理器实例。如果您只想修改超时，那么定义以下内容就足够了：

```
<bean id="lifecycleProcessor" class="org.springframework.context.support.DefaultLifecycleProcessor">
    <!-- timeout value in milliseconds -->
    <property name="timeoutPerShutdownPhase" value="10000"/>
</bean>
```

如上所述，LifecycleProcessor接口还定义了用于刷新和关闭上下文的回调方法。后者将简单地驱动关闭过程，就好像已经显式调用了stop（），但是当上下文关闭时会发生。另一方面，“refresh”回调启用了SmartLifecycle bean的另一个功能。刷新上下文（在所有对象都已实例化并初始化之后），将调用该回调，此时默认生命周期处理器将检查每个SmartLifecycle对象的isAutoStartup（）方法返回的布尔值。如果为“true”，则该对象将在该点启动，而不是等待显式调用上下文或其自己的start（）方法（与上下文刷新不同，上下文启动不会自动发生在标准上下文实现中）。“phase”值以及任何“depends-on”关系将以与上述相同的方式确定启动顺序。

