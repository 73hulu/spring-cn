依赖注入\(DI\)是一个对象定义其依赖关系的过程，也就是说，对象只能通过构造函数参数、工厂方法的参数或在对象实例被构造或从工厂方法返回后设置的属性来定义它们所工作的其他对象。然后容器在创建bean时注入这些依赖项。这个过程基本上是反向的，因此名称Inversion of Control（IoC），bean本身通过使用类的直接构造或服务定位器模式来控制其依赖项的实例化或位置。

使用DI原则的代码更简洁，当对象具有依赖关系时，解耦会更有效。该对象不查找其依赖项，也不知道依赖项的位置或类。因此，类变得更容易测试，特别是当依赖关系在接口或抽象基类上时，这允许在单元测试中使用存根或模拟实现。

DI存在两种主要变体，[Constructor-based dependency injection](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-constructor-injection)和[Setter-based dependency injection](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-setter-injection)。

.

