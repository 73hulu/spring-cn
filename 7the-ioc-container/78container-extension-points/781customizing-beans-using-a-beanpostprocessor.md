`BeanPostProcessor`接口定义了可以实现的回调方法，以提供您自己的（或覆盖容器的默认）实例化逻辑，依赖关系解析逻辑等。如果要在Spring容器完成实例化，配置和初始化bean之后实现某些自定义逻辑，则可以插入一个或多个BeanPostProcessor实现。

您可以配置多个BeanPostProcessor实例，并且可以通过设置order属性来控制这些BeanPostProcessors的执行顺序。仅当BeanPostProcessor实现Ordered接口时，才能设置此属性；如果您编写自己的BeanPostProcessor，则应考虑实现Ordered接口。有关更多详细信息，请参阅BeanPostProcessor和Ordered接口的javadoc。另请参阅下面有关BeanPostProcessors编程注册[programmatic registration of](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-programmatically-registering-beanpostprocessors)的说明。

> beanpostprocessor对bean\(或对象\)实例进行操作;也就是说，Spring IoC容器实例化一个bean实例，然后beanpostprocessor执行它们的工作。
>
> BeanPostProcessors的范围是每个容器。这仅在您使用容器层次结构时才相关。如果在一个容器中定义BeanPostProcessor，它将只对该容器中的bean进行后处理。换句话说，在一个容器中定义的bean不会被另一个容器中定义的BeanPostProcessor进行后处理，即使两个容器都是同一层次结构的一部分。
>
> 要更改实际的bean定义（即定义bean的计划），您需要使用BeanFactoryPostProcessor，如[Section 7.8.2, “Customizing configuration metadata with a BeanFactoryPostProcessor”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-extension-factory-postprocessors)

org.springframework.beans.factory.config.BeanPostProcessor接口恰好包含两个回调方法。当这样一个类注册为容器的后处理器，容器创建每个bean实例，后处理器从容器得到一个回调容器在初始化方法之前\(如InitializingBean afterPropertiesSet\(\)和任何宣布init方法\)以及任何bean初始化回调之后都会被调用。后处理器可以对bean实例执行任何操作，包括完全忽略回调。 bean后处理器通常检查回调接口或者可以用代理包装bean。一些Spring AOP基础结构类实现为bean后处理器，以便提供代理包装逻辑。

`ApplicationContext`自动检测在实现bean后处理器接口的配置元数据中定义的任何bean。`ApplicationContext`将这些bean注册为后处理器，以便稍后在创建bean时调用它们。Bean后处理器可以像任何其他bean一样部署在容器中。

请注意，在配置类上使用`@Bean`工厂方法声明`BeanPostProcessor`时，工厂方法的返回类型应该是实现类本身，或者至少是`org.springframework.beans.factory.config.BeanPostProcessor`接口，清楚地表明 该bean的后处理器性质。否则，`ApplicationContext`将无法在完全创建之前按类型自动检测它。由于`BeanPostProcessor`需要尽早实例化以便应用于上下文中其他bean的初始化，因此这种早期类型检测至关重要。

> 虽然推荐的BeanPostProcessor注册方法是通过ApplicationContext自动检测\(如上所述\)，但是也可以使用addBeanPostProcessor方法以编程方式在ConfigurableBeanFactory注册它们。当需要在注册前评估条件逻辑时，甚至在层次结构中跨上下文复制bean post处理器时，这可能非常有用。但请注意，以编程方式添加的BeanPostProcessors不遵循Ordered接口。这是注册的顺序，它决定了执行的顺序。另请注意，以编程方式注册的BeanPostProcessors始终在通过自动检测注册的BeanPostProcessors之前处理，而不管任何显式排序。
>
> 实现BeanPostProcessor接口的类是特殊的，容器会对它们进行不同的处理。直接引用的所有BeanPostProcessors和bean都会在启动时实例化，这是ApplicationContext的特殊启动阶段的一部分。接下来，以排序的方式注册所有beanpostprocessor，并将其应用于容器中的所有其他bean。因为AOP自动代理是作为BeanPostProcessor本身实现的，所以BeanPostProcessors和它们直接引用的bean都没有资格进行自动代理，因此没有编织方面。对于任何此类bean，您应该看到一条信息性日志消息：“Bean foo不适合所有BeanPostProcessor接口处理（例如：不符合自动代理条件）”。
>
> 注意，如果您使用autowiring或@Resource将bean连接到BeanPostProcessor中，Spring在搜索类型匹配依赖项候选时可能会访问意外的bean，因此无法进行自动代理或其他类型的bean后处理。例如，如果你有一个使用@Resource注释的依赖项，其中field / setter名称不直接对应于bean的声明名称而没有使用name属性，那么Spring将访问其他bean以按类型匹配它们。

以下示例显示如何在ApplicationContext中编写，注册和使用BeanPostProcessors。

#### Example: Hello World, BeanPostProcessor-style

第一个例子说明了基本用法。这个示例显示了一个定制的BeanPostProcessor实现，它在容器创建每个bean时调用toString\(\)方法，并将结果字符串打印到系统控制台。

在下面找到自定义BeanPostProcessor实现类定义：

```
package scripting;

import org.springframework.beans.factory.config.BeanPostProcessor;

public class InstantiationTracingBeanPostProcessor implements BeanPostProcessor {

    // simply return the instantiated bean as-is
    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        return bean; // we could potentially return any object reference here...
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) {
        System.out.println("Bean '" + beanName + "' created : " + bean.toString());
        return bean;
    }
}
```

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:lang="http://www.springframework.org/schema/lang"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/lang
        http://www.springframework.org/schema/lang/spring-lang.xsd">

    <lang:groovy id="messenger"
            script-source="classpath:org/springframework/scripting/groovy/Messenger.groovy">
        <lang:property name="message" value="Fiona Apple Is Just So Dreamy."/>
    </lang:groovy>

    <!--
    when the above bean (messenger) is instantiated, this custom
    BeanPostProcessor implementation will output the fact to the system console
    -->
    <bean class="scripting.InstantiationTracingBeanPostProcessor"/>

</beans>
```

请注意如何简单地定义`InstantiationTracingBeanPostProcessor`。 它甚至没有名称，因为它是一个bean，它可以像任何其他bean一样被依赖注入。（前面的配置还定义了一个由Groovy脚本支持的bean。Spring动态语言支持在第35章动态语言支持一章中有详细介绍。 [Chapter 35, Dynamic language support](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#dynamic-language).）

以下简单Java应用程序执行上述代码和配置：

```
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.scripting.Messenger;

public final class Boot {

    public static void main(final String[] args) throws Exception {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("scripting/beans.xml");
        Messenger messenger = (Messenger) ctx.getBean("messenger");
        System.out.println(messenger);
    }

}
```

上述应用程序的输出类似于以下内容：

Bean 'messenger' created : org.springframework.scripting.groovy.GroovyMessenger@272961

org.springframework.scripting.groovy.GroovyMessenger@272961

#### Example: The RequiredAnnotationBeanPostProcessor

将回调接口或注释与自定义BeanPostProcessor实现结合使用是扩展Spring IoC容器的常用方法。一个例子是Spring的RequiredAnnotationBeanPostProcessor——一个随Spring发行版一起发布的BeanPostProcessor实现，它确保用\(任意\)注释标记的bean上的JavaBean属性实际上\(配置为\)依赖注入一个值。

