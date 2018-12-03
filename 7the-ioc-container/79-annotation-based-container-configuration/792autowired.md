> 在下面的示例中，可以使用JSR 330的@Inject注释代替Spring的@Autowired注释。 有关详细信息，请参见此处[here](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-standard-annotations)

您可以将@Autowired注释应用于构造函数：

```
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

> 注意：在 spring 4.3 框架中，@Autowired构造方法不再是必须的，如果 bean 只定义了一个构造函数。如果有多个构造函数，那么至少要一个注解，以告诉容器改用哪一个。

正如所料，您还可以将@Autowired注释应用于“传统”setter方法：

```
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```

您还可以将注释应用于具有任意名称和/或多个参数的方法：

```
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

您也可以将@Autowired应用于字段，甚至可以将它与构造函数混合使用：

```
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    private MovieCatalog movieCatalog;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

> 确保您的目标组件（例如MovieCatalog，CustomerPreferenceDao）始终按照用于@Autowired注释注入点的类型进行声明。否则，由于在运行时未找到类型匹配，注入可能会失败。
>
> 对于通过类路径扫描找到的XML定义的bean或组件类，容器通常预先知道具体类型。但是，对于@Bean工厂方法，您需要确保声明的返回类型具有足够的表达能力。对于实现多个接口的组件或可能由其实现类型引用的组件，请考虑在工厂方法上声明最具体的返回类型（至少与引用bean的注入点所要求的具体相同）。

通过将注释添加到需要该类型数组的字段或方法，还可以从ApplicationContext提供特定类型的所有bean:

```
public class MovieRecommender {

    @Autowired
    private MovieCatalog[] movieCatalogs;

    // ...
}
```

这同样适用于类型集合：

```
public class MovieRecommender {

    private Set<MovieCatalog> movieCatalogs;

    @Autowired
    public void setMovieCatalogs(Set<MovieCatalog> movieCatalogs) {
        this.movieCatalogs = movieCatalogs;
    }

    // ...
}
```

> 如果希望按特定顺序对数组或列表中的项进行排序，则目标bean可以实现org.springframework.core.Ordered接口或使用@Order或标准@Priority注释。否则，它们的顺序将遵循容器中相应目标bean定义的注册顺序。
>
> @Order注释可以在目标类级别声明，也可以在@Bean方法上声明，每个bean定义可能非常独立（如果多个定义具有相同的bean类）。@Order值可能会影响注入点的优先级，但请注意它们不会影响单例启动顺序，这是由依赖关系和@DependsOn声明确定的正交关注点。
>
> 请注意，标准的javax.annotation.Priority注释在@Bean级别不可用，因为它无法在方法上声明。它的语义可以通过@Order值与每个类型的单个bean上的@Primary一起建模。

即使是有类型的映射，只要预期的键类型是字符串，也可以自动连接。映射值将包含预期类型的所有bean，而键将包含相应的bean名称:

```
public class MovieRecommender {

    private Map<String, MovieCatalog> movieCatalogs;

    @Autowired
    public void setMovieCatalogs(Map<String, MovieCatalog> movieCatalogs) {
        this.movieCatalogs = movieCatalogs;
    }

    // ...
}
```

默认情况下，当没有候选bean可用时，自动连接将失败；默认行为是将带注释的方法、构造函数和字段视为指示所需依赖项。此行为可以更改，如下所示。

```
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired(required = false)
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```

> 每个类只有一个构造函数可以标记为必需的，但可以注解多个非必需的构造函数。在这种情况下，会考虑这些候选者中的每一个，Spring使用最贪婪的构造函数，即依赖最满足的构造函数，具有最大数目的参数。
>
> 建议使用@Autowired的必需属性而不是@Required注释。required属性表示该属性不是自动装配所必需的，如果无法自动装配该属性，则会忽略该属性。另一方面，@ Required更强大，因为它强制执行由容器支持的任何方式设置的属性。 如果未注入任何值，则会引发相应的异常。

或者，您可以通过Java 8的java.util.Optional表达特定依赖项的非必需特性：

```
public class SimpleMovieLister {

    @Autowired
    public void setMovieFinder(Optional<MovieFinder> movieFinder) {
        ...
    }
}
```



