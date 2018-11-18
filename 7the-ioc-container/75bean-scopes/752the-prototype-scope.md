bean的非单例，原型范围部署导致每次发出对该特定bean的请求时都会创建一个新的bean实例。也就是说，bean被注入另一个bean，或者通过对容器的getBean（）方法调用来请求它都会创建一个新的bean实例。**通常，对所有有状态bean使用原型范围，对无状态bean使用单例范围。**

下图说明了Spring原型范围。数据访问对象（DAO）通常不配置为原型，因为典型的DAO不保持任何会话状态; 这个作者更容易重用单例图的核心。

![](/assets/prototype.png)

以下示例将bean定义为XML中的原型：

```
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
```

与其他作用域相比，Spring不管理原型bean的完整生命周期：容器实例化、配置和组装原型对象，并将其交给客户端，而不需要进一步记录原型实例。因此，尽管在所有对象上都调用了初始化生命周期回调方法，但在原型的情况下，不调用配置的销毁生命周期回调。客户端代码必须清理原型范围的对象并释放原型bean所持有的昂贵资源（就是说容器只负责容器实例化、配置和组装原型对象，并将其交给客户端，但是不会调用销毁生命周期需客户端自己处理）。要让Spring容器释放原型范围内的bean所拥有的资源，请尝试使用自定义bean后处理器[bean post-processor](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-extension-bpp)，它包含对需要清理的bean的引用。

