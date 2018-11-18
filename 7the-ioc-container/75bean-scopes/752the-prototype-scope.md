bean的非单例，原型范围部署导致每次发出对该特定bean的请求时都会创建一个新的bean实例。也就是说，bean被注入另一个bean，或者通过对容器的getBean（）方法调用来请求它都会创建一个新的bean实例。**通常，对所有有状态bean使用原型范围，对无状态bean使用单例范围。**

下图说明了Spring原型范围。数据访问对象（DAO）通常不配置为原型，因为典型的DAO不保持任何会话状态; 这个作者更容易重用单例图的核心。

![](/assets/prototype.png)

以下示例将bean定义为XML中的原型：

```
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
```



