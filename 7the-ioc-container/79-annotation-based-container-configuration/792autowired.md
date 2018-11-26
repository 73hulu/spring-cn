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





