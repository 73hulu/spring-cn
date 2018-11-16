默认情况下，`ApplicationContext`实现会急切地创建和配置所有单例bean，作为初始化过程的一部分。通常，这种预先实例化是可取的，因为配置或周围环境中的错误是立即发现的，而不是几小时甚至几天后。 如果不希望出现这种情况，可以通过将bean定义标记为延迟初始化来阻止单例bean的预实例化。延迟初始化的bean告诉IoC容器在第一次请求时创建bean实例，而不是在启动时。

在XML中，此行为由&lt;bean /&gt;元素上的lazy-init属性控制; 例如：

```
<bean id="lazy" class="com.foo.ExpensiveToCreateBean" lazy-init="true"/>
<bean name="not.lazy" class="com.foo.AnotherBean"/>
```

当ApplicationContext使用前面的配置时，在ApplicationContext启动时，不会急切地预先实例化名为lazy的bean，而是急切地预先实例化not.lazy bean。但是，当惰性初始化bean是单例bean的依赖项而不是惰性初始化bean时，ApplicationContext在启动时创建惰性初始化bean，因为它必须满足单例bean的依赖项。惰性初始化的bean被注入到其他地方的单例bean中，而不是懒惰初始化的。

您还可以使用`<beans />`元素上的`default-lazy-init`属性在容器级别控制延迟初始化; 例如：

```
<beans default-lazy-init="true">
    <!-- no beans will be pre-instantiated... -->
</beans>
```



