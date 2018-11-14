`ApplicationContext`是高级工厂的接口，能够维护不同bean及其依赖项的注册表。使用方法`T getBean（String name，Class <T> requiredType）`，您可以检索bean的实例。

ApplicationContext使您可以读取bean定义并按如下方式访问它们：

```
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

使用Groovy配置，bootstrapping看起来非常相似，只是一个不同的上下文实现类，它是Groovy感知的（但也理解XML bean定义）：

```
ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```





