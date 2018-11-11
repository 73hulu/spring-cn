日志记录是Spring非常重要的依赖项，因为：

* 这是唯一的强制性外部依赖
* 每个人都喜欢看到他们使用的工具的一些输出
* Spring与许多其他工具集成在一起，所有这些工具都选择了日志依赖项。

应用程序开发人员的目标之一通常是在整个应用程序\(包括所有外部组件\)的中心位置配置统一的日志记录。由于日志框架有如此多的选择，所以这比以前更困难。

Spring中的强制日志依赖关系是Jakarta Commons logging API \(JCL\)。我们针对JCL进行编译，并使扩展Spring框架的类可以看到JCL日志对象。对于用户来说，所有版本的Spring都使用相同的日志库是很重要的:迁移很容易，因为即使在扩展Spring的应用程序中，向后兼容性也得到了保留。我们采取的方式是使Spring中的一个模块显式地依赖于`commons-logging`公共日志记录\(JCL的规范实现\)，然后在编译时使所有其他模块都依赖于此。例如，如果您正在使用Maven，并且想知道从哪里获得对`commons-logging`公共日志记录的依赖，那么它来自Spring，特别是来自名为`Spring -core`的中心模块。

关于`commons-logging`公共日志记录的好处是您不需要任何其他东西来让您的应用程序工作。它有一个运行时发现算法，可以在类路径中众所周知的位置查找其他日志框架，并使用它认为合适的日志框架\(如果需要，您可以告诉它是哪个\)。如果没有其他可用的日志，那么只需从JDK \(java.util.logging,简称为JUL\)中就可以得到非常漂亮的日志。您应该发现您的Spring应用程序在大多数情况下都可以正常工作，并且可以开箱即用地输出到控制台，这一点很重要。

#### Using Log4j 1.2 or 2.x

> Log4j 1.2已经不再更新L。另外，Log4j 2.3是最后一个兼容Java 6的发行版，带有较新的Log4j 2.x releases需要Java 7+版本。

许多人使用Log4j作为日志框架来进行配置和管理。它是高效和完善的，事实上，我们在构建Spring运行时使用它。Spring还提供了一些配置和初始化Log4j的实用程序，因此在某些模块中它对Log4j有一个可选的编译时依赖关系。

要使Log4j 1.2使用默认的JCL依赖项\(`common -logging`\)，您需要做的就是将Log4j放到classpath上，并为它提供一个配置文件\(`log4j.properties`或llog4j.xml在类路径根中\)。因此，对于Maven用户，这是您的依赖项声明:

```
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.20.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
```

这是一个 log4j.properties示例用于输出到控制台：

```
log4j.rootCategory=INFO, stdout

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %t %c{2}:%L - %m%n

log4j.category.org.springframework.beans.factory=DEBUG
```

使用Log4j 2.x ，对于JCL，您所需要做的就是将Log4j放到类路径上，并为它提供一个配置文件\(log4j2.xml, log4j2.properties, 或[`其他支持的配置格式`](https://logging.apache.org/log4j/2.x/manual/configuration.html)\)。对于Maven用户，需要的最小依赖关系是:

```
<dependencies>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.6.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-jcl</artifactId>
        <version>2.6.2</version>
    </dependency>
</dependencies>
```

如果您还希望让SLF4J委托给Log4j，例如，对于默认使用SLF4J的其他库，还需要以下依赖项:

> **slf4j：**它仅仅是一个为Java程序提供日志输出的统一接口，并不是一个具体的日志实现方案，就比如JDBC一样，只是一种规则而已，所以单独的slf4j是不能工作的，必须搭配其他具体的日志实现方案，比如log4j或者log4j2，类似于Apache Common-Logging，是对不同日志框架提供的一个门面封装。
>
> **common-logging**
>
> 通过动态查找的机制，在程序运行时自动找出真正使用的日志库。由于它使用了ClassLoader寻找和载入底层的日志库， 导致了象OSGI这样的框架无法正常工作，因为OSGI的不同的插件使用自己的ClassLoader。 OSGI的这种机制保证了插件互相独立，然而却使Apache Common-Logging无法工作。
>
> **slf4j**
>
> 在编译时静态绑定真正的Log库,因此可以再OSGI中使用。

```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.6.2</version>
  </dependency>
</dependencies>
```

这是log4j2.xml示例，用书输出日志到控制台

```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Logger name="org.springframework.beans.factory" level="DEBUG"/>
    <Root level="error">
      <AppenderRef ref="Console"/>
    </Root>
  </Loggers>
</Configuration>
```

**避免Commons Logging**

不幸的是，标准公共日志`commons-logging`API中的运行时发现算法虽然对最终用户很方便，但可能存在问题。如果您想避免JCL的标准查找，有两种方法可以关闭它：

* 从`spring-core`模块中排除依赖项\(因为它是唯一明确依赖于公共日志记录的模块`commons-logging`\)
* 依赖于一个特殊的通用日志\(ommons-logging\)依赖关系，这个依赖关系用一个空jar替换了库\(更多细节可以在[SLF4J FAQ](http://slf4j.org/faq.html#excludingJCL)中找到\)
  排除commons-logging，请将以下内容添加到dependencyManagement部分：

```
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.20.RELEASE</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```

由于类路径上没有JCL API的实现，所以这个应用程序目前处于故障状态，因此必须提供一个新的API来修复它。在下一节中，我们将展示如何使用SLF4J提供JCL的替代实现。

#### Using SLF4J with Log4j or Logback

[SLF4J](http://www.slf4j.org/)是其他库常用的Spring的流行API。它通常与Logback一起使用，Logback是SLF4J API的本地实现。

SLF4J提供了对许多常见日志记录框架\(包括Log4j\)的绑定，它也做了相反的工作:连接其他日志框架和自身。因此，要想在Spring中使用SLF4J，您需要用SLF4J- jcl桥来替换公共日志记录`commons-logging`依赖项。完成这些之后，Spring中的日志调用将被转换为SLF4J API的日志调用，因此，如果应用程序中的其他库使用该API，那么就只有一个地方可以配置和管理日志。

一个常见的选择可能是将Spring连接到SLF4J，然后提供从SLF4J到Log4j的显式绑定。您需要提供几个依赖项\(并排除现有的公共日志记录commons-logging\):JCL桥、到Log4j的SLF4j绑定以及Log4j提供者本身。在Maven中，您可以这样做

```
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.20.RELEASE</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
```

SLF4J用户的一个更常见的选择是直接绑定到[Logback](http://logback.qos.ch/)，它使用更少的步骤并生成更少的依赖项。这消除了额外的绑定步骤，因为Logback直接实现了SLF4J，所以您只需要依赖两个库，即jcl-over-slf4j和Logback\)：

```
<dependencies>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.1.7</version>
    </dependency>
</dependencies>
```

**使用JUL\(java.util.logging\)**

在默认情况下，如果类路径上没有检测到Log4j，Commons Logging都将委托给java.util.logging ，记录日志。所以没有特别的依赖关系：只需使用Spring，无需外部依赖即可将日志输出输出到java.util.logging，在独立的应用程序中\(具有JDK级别的自定义或默认的JUL设置\)或应用服务器的日志系统中\(及其系统范围的JUL设置\)。

**WebSphere通用日志**

Spring应用程序可以在提供JCL实现的容器上运行，例如IBM’s WebSphere Application Server \(WAS\)。这本身不会引起问题，但会导致需要理解的两种不同情况:

在“父类优先”类加载器委托模型\(WAS上的缺省值\)中，应用程序将始终采用服务器提供的公有日志记录版本，将其委托给WAS日志记录子系统\(它实际上基于JUL\)。无论是标准的Commons Logging还是JCL-over slf4j桥接器，应用程序提供的JCL的变体都将与任何本地包含的日志提供程序一起被有效地忽略。

使用“父最后”委托模型\(常规Servlet容器中的缺省值，但是WAS上有一个显式的配置选项\)，应用程序提供的Commons Logging变量将被选中，使您能够在应用程序中设置一个本地包含的日志提供程序，例如Log4j或Logback。在没有本地日志提供程序的情况下，常规的Commons Logging默认都会委托给JUL，就像在“父优先”场景中一样，它可以有效地日志到WebSphere的日志子系统。

总之，我们建议在“父最后”模型中部署Spring应用程序，因为它自然允许本地提供者以及服务器的日志子系统。

