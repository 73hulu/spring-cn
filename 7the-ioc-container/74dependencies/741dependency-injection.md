依赖注入\(DI\)是一个对象定义其依赖关系的过程，也就是说，对象只能通过构造函数参数、工厂方法的参数或在对象实例被构造或从工厂方法返回后设置的属性来定义它们所工作的其他对象。然后容器在创建bean时注入这些依赖项。这个过程基本上是反向的，因此名称Inversion of Control（IoC），bean本身通过使用类的直接构造或服务定位器模式来控制其依赖项的实例化或位置。

使用DI原则的代码更简洁，当对象具有依赖关系时，解耦会更有效。该对象不查找其依赖项，也不知道依赖项的位置或类。因此，类变得更容易测试，特别是当依赖关系在接口或抽象基类上时，这允许在单元测试中使用存根或模拟实现。

DI存在两种主要变体，[Constructor-based dependency injection](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-constructor-injection)和[Setter-based dependency injection](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-setter-injection)。

#### Constructor-based dependency injection（基于构造函数的依赖注入）

.基于构造函数的DI由容器调用具有多个参数的构造函数来完成，每个参数表示一个依赖项。调用具有特定参数的静态工厂方法来构造bean几乎是等效的，本讨论同样处理构造函数和静态工厂方法的参数。以下示例显示了一个只能通过构造函数注入进行依赖注入的类。请注意，此类没有什么特别之处，它是一个POJO，它不依赖于容器特定的接口，基类或注释。

```
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on a MovieFinder
    private MovieFinder movieFinder;

    // a constructor so that the Spring container can inject a MovieFinder
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```

##### Constructor argument resolution（构造函数参数解析）

使用参数的类型进行构造函数参数解析匹配。如果bean定义的构造函数参数中不存在潜在的歧义，那么在bean定义中定义构造函数参数的顺序是在实例化bean时将这些参数提供给适当的构造函数的顺序。 考虑以下课程：

```
package x.y;

public class Foo {

    public Foo(Bar bar, Baz baz) {
        // ...
    }
}
```



