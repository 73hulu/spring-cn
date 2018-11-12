Spring Framework 4.0提供了对几个Java 8特性的支持。您可以使用lambda表达式和方法引用和Spring的回调接口。对于`java.time`

\([JSR-310](https://jcp.org/en/jsr/detail?id=310)\)有一流的支持，一些现有的注释被修改为@Repeatable。您还可以使用Java 8的参数名称发现\(基于-parameters编译器标志\)作为启用调试信息编译代码的替代方法。

Spring仍然兼容旧版本的Java和JDK:具体来说，Java SE 6\(具体来说，相当于JDK 6 update 18的最低级别，如2010年1月发布的版本\)及以上版本仍然完全支持。但是，对于基于Spring 4的新启动的开发项目，我们建议使用Java 7或8。

> 到2017年底，JDK 6将逐步淘汰，尽管如此Spring也将支持JDK 6。Oracle和IBM将在2018年终止对JDK 6的所有商业支持。而Spring将保留对整个4.3.x的JDK 6运行时兼容性。我们需要升级到JDK 7或更高版本，以便在此基础上提供进一步的支持:：特别是对于JDK 6特定的bug修复或JDK 7升级解决问题的其他问题。



