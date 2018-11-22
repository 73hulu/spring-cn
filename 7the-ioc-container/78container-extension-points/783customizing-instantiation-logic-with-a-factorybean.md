实现了org.springframework.beans.factory.FactoryBean接口的类自己就是一个factory，可以创建对象。

FactoryBean接口是Spring IoC容器实现自定义实例化逻辑的可插拔点。如果你有一个复杂的初始化代码，用Java表示，而不是（可能）冗长的XML，你可以创建自己的FactoryBean，在该类中编写复杂的初始化，然后将自定义FactoryBean插入容器。

FactoryBean接口提供三种方法：

Object getObject\(\)：返回该工厂创建的对象的实例。实例可以共享，具体取决于此工厂是返回单例还是原型。

boolean isSingleton\(\)：如果此FactoryBean返回单例，则返回true，否则返回false。

Class getObjectType\(\)：返回getObject（）方法返回的对象类型，如果事先不知道类型，则返回null。

FactoryBean概念和接口用于Spring框架中的许多地方;FactoryBean接口的50多个实现与Spring本身一起发布。

当您需要向容器询问实际的FactoryBean实例而不是它生成的bean时，在调用ApplicationContext的getBean（）方法时，使用＆符号（＆）作为bean的id前缀。因此，对于id为myBean的给定FactoryBean，在容器上调用getBean（“myBean”）将返回FactoryBean的产品; 而，调用getBean（“＆myBean”）会返回FactoryBean实例本身。

