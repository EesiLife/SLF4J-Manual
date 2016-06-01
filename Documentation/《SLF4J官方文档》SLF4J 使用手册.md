##SLF4J用户手册


[原文链接](http://www.slf4j.org/manual.html)

Java的简单日志门面（SLF4J）作为一个简单的门面或抽象，用来服务于各种各样的日志框架，比如java.util.logging、logback和log4j。SLF4J允许最终用户在部署时集成自己想要的日志框架。
需要注意的是，你的应用启用SLF4J意味着需要一个额外的依赖：slf4j-api-1.7.19.jar。

**从1.6.0版本起**：如果在class path没有找到绑定，SLF4J将默认一个无操作的实现。

**从1.7.0版本起**：[Logger](http://www.slf4j.org/apidocs/org/slf4j/Logger.html)接口中的打印方法现在提供variants取代了Object[]用来接收[可变参数](http://docs.oracle.com/javase/1.5.0/docs/guide/language/varargs.html)。这个改变意味着SLF4J需要JDK 1.5或更高的版本。Java编译器在底层把方法中的可变参数部分转换成Object[]。因此，编译器生成的Logger接口在1.7.x版本中和它对应的的1.6.x版本中是没有区别的。因此SLF4J得1.7.x版本和1.6.x版本是 完全兼容的。

**从1.7.5版本起**：日志的检索时间有了一个显著的改善，鉴于这个改善，非常鼓励用户迁移到SLF4J 1.7.5或更高的版本。

**从1.7.9版本起**： 通过设置  slf4j.detectLoggerNameMismatch 系统属性为true，SLF4J能自动的[识别不正确的命名的Loggers](http://www.slf4j.org/codes.html#loggerNameMismatch).

-
###Hello World
按照惯例，下面的示例说明最简单的方法使用SLF4J输出”hello world“。他首先获取一个名为”HelloWorld“的记录器，这个记录器用来记录”HelloWorld“。

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```
运行这个示例，首先你需要[下载slf4j发布版](http://www.slf4j.org/download.html)，然后解压。做完这些之后，把 slf4j-api-1.7.19.jar添加到你的class path。

编译运行HelloWorld，控制台将有一下输出：

	SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
	SLF4J: Defaulting to no-operation (NOP) logger implementation
	SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
	
由于在class path下没有找到绑定的slf4j，会打印此警告信息。

一旦你添加一个绑定在你的class path下，这个警告将会消失。假设你添加了 slf4j-simple-1.7.19.jar，你的class path包含：

	slf4j-api-1.7.21.jar
 	slf4j-simple-1.7.21.jar
 	
 编译并运行HelloWorld程序，现在在控制台会有下列输出：
 
 	[main] INFO HelloWorld - Hello World
 	

-
 	
###典型使用模式
下面的示例代码说明了SLF4J典型的使用模式。注意15行上占位符“{}”的使用。查看FAQ中的“什么是记录的最快的方法？”的问题以获取更多细节。

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Wombat {

final Logger logger = LoggerFactory.getLogger(Wombat.class);
Integer t;
Integer oldT;

public void setTemperature(Integer temperature) {
 
    oldT = t;        
    t = temperature;

    logger.debug("Temperature set to {}. Old temperature was {}.", t, oldT);

    if(temperature.intValue() > 50) {     logger.info("Temperature has risen above 50 degrees.");
     }
   }
} 
```

-

###在部署时间日志框架绑定
如前所述，SLF4J支持各种各样的日志框架。SLF4J的发行版附带的几个jar被称为” SLF4J bindings“ ，每个绑定都对应一个支持的框架。

-
**_SLF4J-log4j12-1.7.21.jar_**

绑定[log4j 1.2版本](http://logging.apache.org/log4j/1.2/index.html)，一个广泛使用的日志框架，你也需要在类路劲下放置_log4j.jar_。

-
**_slf4j-log4j12-1.7.21.jar_**

绑定java.util.logging，也被称为JDK 1.4 日志。

-**_slf4j-nop-1.7.21.jar_**

绑定[NOP](http://www.slf4j.org/api/org/slf4j/helpers/NOPLogger.html)，静默状态下丢弃所有的日志。

-
**_slf4j-simple-1.7.21.jar_**

绑定[Simple](http://www.slf4j.org/apidocs/org/slf4j/impl/SimpleLogger.html)实现，输出所有的System.err事件，只有INFO和更高级别的信息会被打印。这个绑定可能在小应用程序的上下文中非常有用.

-
**_slf4j-jcl-1.7.21.jar_**

绑定[Jakarta Commons Logging](http://commons.apache.org/logging/)，此种绑定会将所有SLF4J日志委托给JCL。

-
**_logback-classic-1.0.13.jar (需要logback-core-1.0.13.jar)_**

**本地实现**：也有SLF4J绑定到外部的SLF4J项目。例如，[logback](http://logback.qos.ch/)本地实现SLF4J。Logback的[ch.qos.logback.classic.Logger](http://logback.qos.ch/apidocs/ch/qos/logback/classic/Logger.html)类是SLF4J的[org.slf4j.Logger](http://www.slf4j.org/apidocs/org/slf4j/Logger.html)接口的直接实现。因此，结合logback使用SLF4J时会严格地涉及零内存和计算开销。


切换日志框架，只需要替换class path中的slf4j绑定。比如说，从  java.util.logging切换到 log4j，仅仅把  slf4j-jdk14-1.7.19.jar替换成 slf4j-log4j12-1.7.19.jar。

SLF4J不依赖于特定的类加载机制。事实上，每个SLF4J绑定在编译时硬连接来使用一个指定的日志框架。比如说， slf4j-log4j12-1.7.19.jar在编译时绑定使用log4j。在你的代码中，除  slf4j-api-1.7.19.jar之外，**只能有且仅有一个**你选择的绑定 到正确的class path 路径上。不要在class path 放置多个绑定。下面是一个图表来说明一般的想法。

![图片](http://www.slf4j.org/images/concrete-bindings.png)

SLF4J接口和他们的各种各样的适配器非常简单。大多数熟悉Java语言的开发者应该能在一小时之内读懂代码。不需要类加载的知识。因此，SLF4J没有遭受类加载问题或者 Jakarta Commons Logging (JCL)观察到的内存泄露问题。

鉴于SLF4J接口的简单性和它的部署模型，新的日志框架开发者应该觉得很容易编写SLF4J绑定。

-
###库
为了避免给他们的终端用户强加一个日志框架 ，一些组件和库的作者可能会针对SLF4J接口进行编程。因此，终端用户在部署时可以通过在classpath插入对应的slf4j绑定来选择他们期望的日志框架。并且，可以把在classpath存在的绑定替 换到另外一个，然后重启应用，从而达到切换日志框架的目的，这个方法被证明是非常简单和健壮的。

**SLF4J 1.6.0版本**，如果在class path 没有发现绑定，slf4j-api将默认一个无操作的实现来忽略所有的日志请求。因此，SLF4J从1.6.0版开始发出一个缺少绑定的警告，然后丢弃所有的打印日志请求，而不是抛出 NoClassDefFoundError异 常因为缺少 org.slf4j.impl.StaticLoggerBinder 类 。比如说： Wombat 一些基础框架依赖于SLF4J来打印日志。为了避免强加一个日志框架给终端用户，Wombat  包含  slf4j-api.jar但是没有绑定。在class path 没有SLF4J绑定的情 况下，Wombat的发行版依然能开箱即用，不需要终端用户下载从SLF4J网站下载一个绑定。仅仅当终端用户开启日志的时候，将需要安装他选择的日志框架对应的SLF4J绑定。

**基本准则**：**内置的组件比如库和框架不应该声明任何SLF4J的依赖，但是依赖于SLF4J-api**。当一个库在一个指定的绑定声明一个传递依赖，这个强加于终端用户的绑定否定了SLF4J的目的。注意，在绑定上声明一个非传递性的依赖，比如说对测试，不影响终端用户。

内嵌组件中SLF4J的使用也在FAQ中进行讨论。有关系的章节：[日志配置logging configuration](http://www.slf4j.org/faq.html#configure_logging),[依赖减少dependency reduction](http://www.slf4j.org/faq.html#optional_dependency)和[测试testing](http://www.slf4j.org/faq.html#optional_dependency).

-
###声明项目的日志依赖
鉴于Maven的传递依赖规则，“常规”项目（并非库或框架）声明记录的依赖可以通过一个单一的依赖性声明来完成。

**LOGBACK-CLASSIC**： 如果你希望使用 logback-classic 作为底层的日志框架，你需要做的所有事情就是声明”ch.qos.logback:logback-classic“依赖到你的pom.xml文件中。除了 logback-classic-1.0.13.jar之外，它将拉取 slf4j-api-1.7.19.jar和 logback-core-1.0.13.jar 到你的项目中。注意，显式的声明  logback-core-1.0.13或  slf4j-api-1.7.19.jar 没有错，并且可能需要强加一个正确的版本。

```java
<dependency> 
  <groupId>ch.qos.logback</groupId>
  <artifactId>logback-classic</artifactId>
  <version>1.0.13</version>
</dependency>
```
**LOG4J**：如果你希望使用log4j作为底层的日志框架，你需要做的所有事情就是声明” org.slf4j:slf4j-log4j12“依赖到你的pom.xml文件中。除了 slf4j-log4j12-1.7.19.jar之外，它将拉取 slf4j-api-1.7.19.jar和  log4j-1.2.17.jar到你的项目 中。注意，显示地声明一个依赖  log4j-1.2.17.jar或 slf4j-api-1.7.19.jar 没有错。并且可能需要强加一个正确的版本.

```java
<dependency> 
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>1.7.21</version>
</dependency>
```
**JAVA.UTIL.LOGGING**：如果你希望使用  java.util.logging 作为底层的日志框架，你需要做的所有事情就是声明” org.slf4j:slf4j-jdk14“依赖到你的pom.xml文件中。除了 slf4j-jdk14-1.7.19.jar之外，它将拉取  slf4j-api-1.7.19.jar到你 的项目中。注意，显示地声明一个依赖  slf4j-api-1.7.19.jar  没有错。并且可能需要强加一个正确的版本。

```java
<dependency> 
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-jdk14</artifactId>
  <version>1.7.21</version>
</dependency>
```

-
###二进制兼容性
一个SLF4J绑定指定一个具体的依赖用来在底层日志框架绑定slf4j，比如说 slf4j-jdk14.jar或 slf4j-log4j12.jar。

不同版本的 slf4j-api.jar和SLF4J绑定混合在一起可能会引起问题。比如说，如果你使用  slf4j-api-1.7.19.jar，然后你应该使用 slf4j-simple-1.7.19.jar，使用 slf4j-simple-1.5.5.jar将不会正常工作。

然而，从客户端的观点来看，所有版本的 slf4j-api都是兼容的。使用 slf4j-api-N.jar编译的客户端代将和  slf4j-api-M.jar一起完美运行，N和M都是任意的。你仅仅需要确保你绑定的版本和 slf4j-api.jar 匹配。你不需要担心项目中给定的依 赖的slf4j-api.jar的版本。你可以使用任何版本的 slf4j-api.jar，只要 slf4j-api.jar的版本和它的绑定匹配，就不会有问题。

在初始化的时间，如果SLF4J怀疑可能有不匹配问题。将会发出关于不匹配的警告信息。

-
###通过SLF4J整合日志
通常，一个给定的项目依赖于各种各样的组件，这些组件依赖的日志API不是SLF4J。一个项目依赖于一个JCL、java.util.logging、log4j和SLF4J是很常见的。然后通过一个单独的方式来统一日志变得让人满意。SLF4J通过提供对JCL、

java.util.logging、和slf4j的桥接模块来满足这个普通的用例。更详细的内容，请参考[传统桥接APIs-Bridging legacy APIs](http://www.slf4j.org/legacy.html).

-
###映射诊断上下文支持（MDC）
“Mapped Diagnostic Context”本质上是日志框架包含的一个map，应用程序代码提供了key-value对，这个键值对能被日志框架插入到日志信息中去。MDC数据在过滤信息或触发某些操作时是非常有用的。

SLF4J支持MDC，如果一个底层的日志框架提供了MDC功能，SLF4J将委托给底层日志框架的MDC。注意，现在仅仅只有log4j和logback提供了MDC功能。如果底层日志框架不支持MDC，比如说java.util.loggin，SLF4J将存储MDC数 据，但是里面的数据需要用户通过代码来获取。

因此，作为一个SLF4J使用者，能利用当 log4j和logback存在时的 MDC信息，但是不能强迫用户依赖这些日志框架。

关于MDC的更多信息参见[MDC章节](http://logback.qos.ch/manual/mdc.html).

-
###执行摘要
| 优点       | 描述   |
| --------   | -----:  | 
| 在部署时选择日志框架    | 所需的日志框架可以在部署时通过你的类路径中插入相应的jar文件（绑定）植入。 | 
| 快速失败操作     |   由于该类由JVM加载的方式，框架结合将很早地被自动验证。 如果SLF4J找不到类路径上的绑定就会发出一个警告消息，并且默认为无操作实现。  |
| 绑定流行日志框架        |    SLF4J支持流行的日志框架，即log4j的，java.util.logging中，简单的记录和NOP。 该logback项目支持本地SLF4J。| 
| 传统桥接日志APIs        |    通过SLF4J实现JCL，如jcl-over-slf4j.jar，将允许项目迁移到SLF4J，没有破坏使用JCL的软件的兼容性。同样的，log4j-over-slf4j.jar和jul-to-slf4j模块重定向log4j到SLF4J。查看Bridging legacy APIs 获取更多信息 |
| 迁移源代码    | SLF4J-迁移工具可以帮助您迁移源代码以使用SLF4J。 | 
| 参数化的日志消息的支持  | 所有的SLF4J绑定支持参数化的日志信息。并且可以显著提高性能。| 


	

