只管理单个bean的一个共享实例，对于id或id匹配该bean定义的bean的所有请求都会导致Spring容器返回一个特定的bean实例。

换句话说，当您定义一个bean定义并且它的范围是一个单例时，Spring IoC容器只创建该bean定义的对象的一个实例。此单个实例存储在此类单例bean的缓存中，并且该命名Bean的所有后续请求和引用都将返回缓存对象。

![](/assets/singleton.png)

Spring的单例bean概念不同于Gang of Four（GoF）模式书中定义的Singleton模式。GoF Singleton对对象的范围进行硬编码，使得每个ClassLoader创建一个且只有一个特定类的实例。Spring 单例的范围最好描述为每个容器和每个bean。这意味着，如果您在单个Spring容器中为特定的类定义了一个bean，那么Spring容器将创建由该bean定义定义的类的一个且只有一个实例。单例范围是Spring中的默认范围。 要将bean定义为XML中的单例，您可以编写，例如：

```
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- the following is equivalent, though redundant (singleton scope is the default) -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```



