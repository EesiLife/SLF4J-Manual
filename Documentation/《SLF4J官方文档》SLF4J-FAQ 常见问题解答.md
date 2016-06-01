##《SLF4J官方文档》SLF4J-FAQ 常见问题解答
###一般性问题
	1.什么是SLF4J？
	2.什么时候应该使用SLF4J?
	3.SLF4J仍是另一个日志外观吗？
	4.如果SLF4J可修复JCL,那为什么不在JCL里加入修复而是创建一个新项目？
	5.使用SLF4J时，我必须重新编译我的应用以转换到一个不同的日志系统吗？
	6.SLF4J的要求是什么？
	7.SLF4J向后兼容版本吗？
	8.使用SLF4J时遇到访问权限错误，原因是什么？
	9.为什么SLF4J是在X11类型许可证下许可而不是Apache软件许可？
	10.在哪里能获得特定的SLF4J绑定？
	11.我的库应该尝试配置logging吗？
	12.为了减小我们软件的依赖库数量，我们想让SLF4J成为一个可选择的依赖包。这个是好主意吗？
	13.Maven传递依赖怎么样？
	14.如何排除依赖Maven的commons-logging?
-
	
###关于SLF4J API
	1.为什么日志器接口的打印方法不接收对象类型的消息，但只收String类型消息？
	2.我可以输出一个没有消息的异常日志吗？
	3.输出日志（关闭日志）的最快方法是什么？
	4.如何打印单独（可能是复杂的）对象的字符串内容日志？
	5.为什么slf4j.Logger接口没有FATAL级别的方法？
	6.为什么TRACED级别的日志只在SLF4J 1.4.0版本介绍？
	7.SLF4J日志API支持I18N（国际化）吗？
	8.不通过LoggerFactory里的静态方法，可以重新获取日志器吗？
	9.存在错误/抛出异常时，可以参数化日志声明吗？
-
###实现SLF4J API
	1.如何使我的日志底层SLF4J可兼容？
	2.如何使我的日志系统添加支持Marker接口？
	3.SLF4J的版本检查机制如何工作？
-
###关于日志的一般性问题
	1.类的日志器成员变量应该声明为静态变量吗？
	2.类里的声明日志器有推荐的习语吗？
-
##一般性问题
####1.什么是SLF4J？
SLF4J是一个简单的日志系统外观，它允许终端用户在部署时植入所需日志系统。

-
####2.什么时候应该使用SLF4J?
简单而言，为库和其他嵌入组件的日志需求，它们应该考虑下SLF4J，因为库不能强加终端用户日志框架的选择。另外，独立应用使用SLF4J不一定有意义。独立应用可以直接援引他们选择的日志框架。对于logback，问题是没有实际意义的，因为logback通过SLF4J暴露它的logger API。

SLF4J只是个外观，意味着它不提供一个完整的日志方案。例如配置输出源或者设置日志级别的操作不能和SLF4J执行。因此，在某个时间点上，任何微不足道的应用将需要直接援引底层日志系统。换句话说，完全对立的API底层日志系统是不可能给独立应用程序使用的。然而，SLF4J减少这种依赖的影响到无痛苦级别。

假设你的CRM应用用log4j来记录日志。然后，你的一个重要客户端请求日志要通过JDK1.4执行日志记录。如果你的应用充斥着数以千计的直接log4j调用，转移到JDK1.4的调用将相对是一个冗长和容易出错的过程。更糟的是，你可能需要维护两个版本的CRM软件。你已经引用SLF4J API而不失log4J,通过替换二者的jar文件，迁移可在几分钟内完成。

SLF4J允许组件开发人员推迟终端用户的日志系统的选择，但最终仍需要做出一个选择。

-
####3.SLF4J仍是另一个日志外观吗？

**SLF4J在概念上和JCL非常相似。因此它可以被认为是另一个日志外观。但是，SLF4J在设计上更简单和更健壮。简而言之，SLF4J避免了植入JCL的类加载问题。**

-
####4.如果SLF4J可修复JCL,那为什么不在JCL里加入修复而是创建一个新项目？

这是非常好的问题。首先，SLF4J静态绑定方法非常简单，甚至很可笑。很难说服开发者这个方法的有效性。仅仅在SLF4J发布后和开始变得被接收后，它才在相关社区里获得尊重。

其次，SLF4J提出2个改进手段，这都趋向被低估了。用务实的方法，参数化日志消息解决了一个跟日志性能联系的重要问题。标记对象，由org.slf4j.Logger接口支持,为高级日志系统的采用铺路，如果必要的话，同时让放开转回更多传统日志系统的大门。

-
####5.使用SLF4J时，我必须重新编译我的应用以转换到一个不同的日志系统吗？

不，你不需要重新编译你的应用。你可以通过移除之前的SLF4J绑定，然后将它替换为你选择的绑定，就能转换到不同的日志系统了。

例如，如果你在使用NOP实现，希望转换到log4j 1.2版本，在你的类目录下用 slf4j-log4j12.jar 替换slf4j-nop.jar，但也别忘了添加log4j-1.2.x.jar 。像转换到JDK 1.4 logging?只需用slf4j-jdk14.jar替换slf4j-log4j12.jar。

-
####6.SLF4J的要求是什么？

1.7.0版本SLF4J需要JDK1.5以上。早期的SLF4J版本，像SLF4J 1.4,1.5和1.6，要求JDK1.4。

| Binding     | Requirements       |
| ------------- |:-------------:| 
| slf4j-nop     | JDK 1.5 |
| slf4j-simple  | JDK 1.5     |
| slf4j-log4j12 | JDK 1.5，加入任何其他所需的依赖包取决于使用的log4j输出源| 
| slf4j-jdk14  | JDK 1.5以上版本     |
| logback-classic  | JDK 1.5以上版本，加入任何其他所需的依赖包取决于使用的log4j输出源     |

-
####7.SLF4J向后兼容版本吗？

从客户端角度，SLF4J API对所有版本都向后兼容。这意味着你能从SLF4J版本1.0升级到任何以后的版本，没有任何问题。对于slf4j-api版本N和slf4j-api

版本M, 版本N编译的代码将和版本M编译的代码一起正常运作。**迄今为止, slf4j-api里的二进制兼容性从来没有被打破。**

然而，从客户端角度，SLF4J API非常稳定时，SLF4J绑定，比如slf4j-simple.jar或slf4j-log4j12.jar，需要一个特定版本的SLF4J-API。混合不同版本的slf4j可能会出现问题和强烈不提倡的。例如，如果你正在使用slf4j-api-1.5.6.jar，然后你也应该使用slf4j-simple-1.5.6.jar，使用slf4j-simple-1.4.2.jar将不会工作。

在初始化时时，如果SLF4J推测可能有版本不匹配问题，它将发出一个关于不匹配的警告。

-
####8.使用SLF4J时遇到访问权限错误，原因是什么？

如下是错误详情：

```java
Exception in thread "main" 
java.lang.IllegalAccessError: tried to access field
org.slf4j.impl.StaticLoggerBinder.SINGLETON from class
org.slf4j.LoggerFactory at
org.slf4j.LoggerFactory.<clinit>(LoggerFactory.java:60)
```
这个问题是由LoggerFactory类的静态初始化尝试直接访问org.slf4j.impl.StaticLoggerBinder的单例变量造成的。这在SLF4J 1.5.5和之前的版本允许，在1.5.6及以后的版本，单例变量已经被标记为私有访问权限了。

如果你遇到了上述的错误，你可以用老版本的slf4j-api，比如1.4.3，同时使用一个新版本的slf4j绑定，比如1.5.6。通常，当你的Maven pom.xml文件使用hibernate 3.3.0，在slf4j-api 1.4.2版本上声明一个依赖时，将发生上述异常。如果你的pom.xml在slf4j绑定上声明依赖，比方说slf4j-log4j12 版本1.5.6，你会触发非法访问错误。

查看Maven里那个slf4j-api版本被拉入，用下述maven依赖植入。

```java
mvndependency:tree
```
如果你使用Eclipse,请不要依靠m2eclipse显示的关系树。

如果你的pom.xml明确在slf4j-api上声明了依赖，这个版本的api匹配已声明的绑定，将使这个问题消失。

也请读[向后兼容性](http://www.slf4j.org/faq.html#compatibility)的FAQ，以获得更多普遍性解释。

-
####9.为什么SLF4J是在X11类型许可证下许可而不是Apache软件许可？
SLF4J是在X11类型许可证下许可而不是Apache软件许可，这是因为X11许可是被Apache软件基金会以及自由软件基金会的各自许可认为是科兼容的。
10.在哪里能获得特定的SLF4J绑定？

-
####10.在哪里能获得特定的SLF4J绑定？
给[SimpleLogger](http://www.slf4j.org/api/org/slf4j/impl/SimpleLogger.html)、[NOPLogger](http://www.slf4j.org/api/org/slf4j/impl/NOPLogger.html)、[og4jLoggerAdapter](http://www.slf4j.org/api/org/slf4j/impl/Log4jLoggerAdapter.html)和[JDK14LoggerAdapter](http://www.slf4j.org/api/org/slf4j/impl/JDK14LoggerAdapter.html)的SLF4J绑定包含在slf4j-nop.jar, slf4j-simple.jar,slf4j-log4j12.jar, and slf4j-jdk14.jar文件里。

给logback-classic的绑定附带在[logback distribution](http://logback.qos.ch/download.html)上。对于其他所有的绑定，logback-classice绑定需要slf4j-api.jar。

-
####11.我的库应该尝试配置logging吗？

**嵌入的组件，比如库，不进不需要配置底层日志框架，**它们也不应该做这些。它们应该引入SLF4j来记录日志，但也应该让终端用户配置日志环境。当嵌入的组件尝试在自己配置日志时，它们常常会覆盖终端用户的意愿。在一天的结束时，终端用户不得不读日志，处理日志。她应该是决定自己想要的日志配置的人。

-
####12.为了减小我们软件的依赖库数量，我们想让SLF4J成为一个可选择的依赖包。这个是好主意吗？

当一个软件工程到达它需要设计日志策略的点时，[这个问题会提起](http://stackoverflow.com/questions/978670/is-it-worth-wrapping-a-logging-framework-in-an-additional-layer)。

让Wombat成为一个具有极少依赖的软件库。如果SLF4J被选作为Wombat的日志API，那么一个新的slf4j-api.jar依赖库将被加入带Wombat的依赖包列表中。由于写日志的包装并不难，一些开发者冒险包装SLF4J，只有当它已经出现在类路径中时链接它，使得SLF4J成为Wombat的一个可选择的依赖库。为了解决这个依赖问题，包装将被从SLF4J的API中隔离Wombat,以确保Wombat中的日志不会过时的。

另外，任何SLF4J-包装都取决于SLF4J得定义。它们一定有相同的通用api。在将来，如果出现一个新的、明显不同的日志API,和直接使用SLF4J的代码一样，使用包装器的代码也将同样很难移植到新的API.因此，包装器不可能是面向未来的代码，但在SLF4J上增加了一个额外的间接连接，使得这更复杂，这本身就是一中间接连接。

**脆弱性增加**实际上比这个还遭。包装器需要依赖随时变化的一些内部SLF4J接口，这违背了面向客户端API永不改变的原则。因此，包装器通常依赖一些同他们一起编译的主要版本。针对SLF4J 1.5.x版本编译的包装器将不会同SLF4J 版本1.6工作，但是使用org.slf4j.Logger, LoggerFactory, MarkerFactory, org.slf4j.Marker和MDC的客户端代码将和任何版本SLF4J 1.0及以后的版本正常工作。合理地假设在大部分工程中，Wombat将是许多依赖库中的一个。如果每个依赖库有自己日志包装器，那么大概每个包装器需要独立配置。因此，不只是处理SLF4J的日志框架，Wombat的用户也必须处理Wombat的日志包装器。

为了使SLF4J可选择，每个框架提出的自己的包装器将加剧这个问题。（配置或处理5个不同的日志包装器将很不愉快，也不招人喜欢）

[Velocity项目采纳的日志策略](http://velocity.apache.org/engine/devel/developer-guide.html#Configuring_Logging)是一个“自定义日志抽象”反模式的好例子。通过采用一个独立的日志抽象的策略，Velocity开发者已经使自己的开发更复杂，但更重要的是，他们让开发对他们的用户更难。

一些项目试着在类路劲上检测SLF4J的存在性，如果存在的话就转换到SLF4J.虽然这个方法看上去足够的透明，但是它将导致错误的位置信息。底层日志框架将打印包装器的位置（类名和行数）而不是打印真正调用者的位置。还有就是除了参数化日志、像SLF4J支持MDC和标记的API覆盖问题。尽管有人可以在数小时内提出一个似乎可运行的SLF4J包装器，随着时间的推移，许多技术问题将出现，这是Wombat开发者不得不处理的问题。注意SLF4J已经发展了好几年了，有260个bug报告提起它。

基于以上原因，框架的开发者应该抵制自己写的日志包装的诱惑。 它不仅是浪费开发者时间，对上述日志的用户，这实际上让开发更困难，使日志代码自相矛盾地更容易改变。

-
####13.Maven传递依赖怎么样？

作为使用Maven构建库作者，你可能想使用绑定来测试你的应用程序，比如SLF4J-log4j12或的logback经典，对用户不强制log4j或logback-classic作为依赖。 这是比较容易做到的。

基于你的库的代码依赖于SLF4J API，你需要声明SLF4J的API作为一个编译时（默认范围）的依赖。

```java
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>1.7.21</version>
</dependency>
```

通过声明SLF4J绑定依赖为“测试”的范围，可以实现限制在测试绑定使用的SLF4J的传递。例如:

```java
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>1.7.21</version>
  <scope>test</scope>
</dependency>
```
因此，至于你的用户要导出SLF4J的API作为库的传递依赖，但不是任何SLF4J绑定或任何底层日志系统。

需要注意的是由于SLF4J 1.6版，在没有SLF4J绑定，SLF4J的API将默认为无无操作实现。

-
####14.如何排除依赖Maven的commons-logging?

####替代1）明确排除

很多使用Maven的软件项目声明commons-logging作为一个依赖。因此，如果你希望迁移到SLF4J或使用jcl-over-slf4j,你需要排除项目中所有依赖里的comms-logging，这些依赖库传递依赖common-logging。[依赖排除](http://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)在Maven文件里已有描述。为分布在几个pom.xml里的多个依赖明确地排除common-logging，可能是一个笨重的和相对容易出错的过程。

####替代2）规定的范围

在项目的pom.xml文件中所提供的范围内，通过声明Commons-logging，它可以像依赖一样简单、方便地排除。实际的commons-logging类将由jcl-over-slf4j提供。 这转化为以下POM文件片段：

```java
<dependency>
  <groupId>commons-logging</groupId>
  <artifactId>commons-logging</artifactId>
  <version>1.1.1</version>
  <scope>provided</scope>
</dependency>
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>jcl-over-slf4j</artifactId>
  <version>1.7.21</version>
</dependency>
```

第一依赖性声明本质规定commons-logging会通过你的环境“以某种方式”提供。第二个声明引入jcl-over-slf4j到你的项目中。 作为jcl-over-slf4j是commons-logging完美的二进制兼容更换，第一个断言为真。

不幸的是，虽然在规定范围内声明的commons-logging能够完成任务，你的IDE，比如Eclipse中，仍将在项目的类路径上放置commons-logging.jar，通过你的IDE可以看到。 您将需要确保jcl-over-slf4j前，您的IDE 的commons-logging.jar的是可见的。

####替代3）空构件

另一种方法是依靠一个空的commons-logging.jar构件。这个聪明的[办法首先](http://day-to-day-stuff.blogspot.com/2007/10/announcement-version-99-does-not-exist.html)由Erik van Oosten想出和最初支持。

空构件可从一个http://version99.qos.ch高可用性Maven仓库，复制在位于不同地域的多个主机。

下面的声明添加version99库设定的Maven的搜索远程资源库。 该存储库中包含的commons-logging和log4j的空构件。 顺便说一句，如果你使用的version99库，请在<version99 AT qos.ch>给我们一行。

```java
<repositories>
  <repository>
    <id>version99</id>
    <!-- highly available repository serving empty artifacts -->
    <url>http://version99.qos.ch/</url>
  </repository>
</repositories>
```
在你的项目里的<dependencyManagement>部分，声明99-empty版本中的commons-logging的将为commons-logging指向所有传递的依赖以输入版本9999-empty，从而很好地解决的commons-logging排斥问题。对于commons-logging的类将由jcl-over-slf4j提供。 以下行声明的commons-logging版本99-empty（在依赖管理部分），并声明jcl-over-slf4j作为一个依赖。

```java
<dependencyManagement>
   <dependencies>
     <dependency>
       <groupId>commons-logging</groupId>
       <artifactId>commons-logging</artifactId>
       <version><strong>99-empty</strong></version>
     </dependency>
     ... other declarations...
   </dependencies>
 </dependencyManagement>
 <!-- Do not forget to declare a dependency on jcl-over-slf4j in the        -->
 <!-- dependencies section. Note that the dependency on commons-logging     -->
 <!-- will be imported transitively. You don't have to declare it yourself. -->
 <dependencies>
   <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>jcl-over-slf4j</artifactId>
     <version>1.7.21</version>
   </dependency>
   ... other dependency declarations
 </dependencies>
```

-
##关于SLF4J API
####1.为什么日志器接口的打印方法不接收对象类型的消息，但只接收String类型消息？

在SLF4J 1.0beta4版里，[Logger interface](http://www.slf4j.org/api/org/slf4j/Logger.html)中例如debug(),info(),warn()的打印方法被修改以用于只接收String类型的消息，而不是Object类型的消息。

因此，DEBUG级别的打印方法设置变为：

```java
debug(Stringmsg);
debug(String format,Objectarg);
debug(String format,Object arg1,Object arg2);
debug(Stringmsg,Throwable t);
```

之前，上述方法中的第一个参数是Object类型。

这个改变强制使人认为日志系统是关于装饰和处理String类型消息，而不是关于任何（Object）类型。

同样重要的是，新的一套签名方法提供重载方法间的透明区别，而以前选择 被调用的Java方法，由于Java的重载规则通常总是不易遵循。

很容易犯错误。例如，之前这样写是合法的:

```java
logger.debug(newException("some error"));
``` 

不幸的是，上述调用不会打印堆栈跟踪的异常。因此，潜在的关键信息片可能会丢失。当第一个参数被限制为String类型时，只有如下方法： 

```java
debug(Stringmsg,Throwable t);
``` 

可用于记录异常，注意，此方法可确保每个日志异常是伴随着一个描述性的消息。

-
####2.我可以输出一个没有伴随消息的异常日志吗？

简言之，不可以。

如果e是一个异常，你想输出一个ERROR级别的异常，你必须增加一个伴随消息。例如：

```java
logger.debug(newException("some error"));
```
你可能合理地辩解不是所有的异常都有一个有含义的消息伴随着它们。此外，良好的异常应该已经包含一个自我解释说明。因此，伴随的消息可以被认为是多余的。

当这些参数是有效参数时，有3个相反的参数也是值得考虑的。首先，在很多场合，但不是所有场合，伴随消息可以很好滴传达有用的信息，在异常里补充描述。通常情况下，在其中异常日志点，开发者获得比在异常被抛出点更多的上下文信息。其次，很难或多或少想象普通消息，例如”Exception caught”, “Exception follows”，可能会用作给错误调用的第一个参数（String msg,Throwable t）。第三，许多日志输出格式显示一行消息，在紧跟着的一行输出异常。因此，消息行没有消息，看起来不一致。

简言之，如果允许用户打印一个没有伴随消息的异常，这将是日志系统的工作：创造一个消息。这实际上是java.util.logging包中的[throwing方法（String sourceClass, String sourceMethod, Throwable thrown）](http://tinyurl.com/cr9kg)所做的。（它取决于自身的伴随消息是字符串“THROW”。）

开始时可能会奇怪地要求一个伴随消息以记录一个异常。 然而，这是在所有的log4j的衍生系统，如java.util.logging，logkit等，当然还有的log4j本身的通行做法。 如此看来，目前的共识认为需要一个伴随的消息是一个很好的做法（TM）。

####3.输出日志（关闭日志）的最快方法是什么？

SLF4J提出一个叫参数化日志的高级特点，可以显著提升有缺陷的日志语句的日志性能。

对于一些Logger logger，写作，

```java
logger.debug("Entry number: "+i+" is "+String.valueOf(entry[i]));
```
引起构造消息参数的开销，就是转换integer i和entry[i]为String,和连接中间的字符串。这一点，不管消息是否记录。

一种可能的方法，以避免构造参数的开销是通过测试中包裹日志语句。例如

```java
if(logger.isDebugEnabled()){
    logger.debug("Entry number: "+i+" is "+String.valueOf(entry[i]));
}
```
如果logger关闭调试,这样的话就不会引起构造参数的开销。在另一方面，如果logger对DEBUG级别开启，无论logger是否开启，都会引起开销，而且是两次：一次是debugEnable开销，一次是debug里的开销。这是微不足道的开销，因为评估日志器花费的时间不到实际过程中记录语句的时间的1%。

**更好的是，使用参数化的消息**

这里存在一个基于消息格式的很方便的选择。假设entry是一个对象，你可以这样写：

```java
Object entry =newSomeObject();
logger.debug("The entry is {}.", entry);
```
在评价是否记录日志后，只有当结论是肯定的时候，日志器实现将格式化消息，并将’{}’替换为entry的字符值。换句话说，假设日志声明是关闭的，这个格式不会引起构建参数的开销，

下面两行代码将产生确切相同的输出。然而，假设关闭日志声明，第二种形式将将至少超过第一种形式的30倍。

```java
logger.debug("The new entry is "+entry+".");
logger.debug("The new entry is {}.", entry);
```
[双参数](http://www.slf4j.org/apidocs/org/slf4j/Logger.html)版本也是可用的。例如，你可以这样写：

```java
logger.debug("The new entry is "+entry+".");
logger.debug("The new entry is {}.", entry);
```
如果需要传递3个或更多的参数时，你要使用[Object…variant](http://www.slf4j.org/apidocs/org/slf4j/Logger.html)打印方法。例如，你可以这样写：

```java
logger.debug("The new entry is {}. It replaces {}.", entry,oldEntry);
```
这中形式引起隐藏的构建一个Objec[]（对象数组）的开销，通常情况下这个开销是很小的。一个和两个参数版本的这种形式不会引起这个隐藏的开销，同时由于这个原因（效率）会完全存在。只有Object…variant的情况下，Slf4j-api会更小/更简洁。

数组形式的参数，包括多维数组，也是支持的。

SLF4J使用自身的消息格式实现，这个和java平台的格式化输出是不一样的。已被证明的事实是：SLF4J的实现平台性能是java平台的十倍，但开销会无标准，弹性也会更小。

摆脱“{}”对

“{}”对叫做格式化锚。它用于指定位置：指定需要用消息样式替换的参数的地方。

SLF4J值关心格式化锚，这就是“{”字符立即跟着字符“}”。因此，如果你的消息包含“{”或“}”字符，不需要做任何特殊处理，除非“}”立即跟着“}”字符。例如：

```java
logger.debug("Set {1,2} differs from {}","3");
```
这将打印“Set {1,2} differs from 3”。

在极其罕见的情况下，“{}”对很自然地出现在你的文本中，你希望禁用格式化锚的特殊意义，你需要将“{”字符替换为“\”，就是反斜杠字符。

只有“\”应该替换。不需要替换“}”字符。例如：

```java
logger.debug("Set \\{} differs from {}","3");
```
 

将会打印为“Set {} differs from 3”。注意在java代码里，反斜杠字符需要写为“\\”。

在少见的情况下：“\{}”在消息中出现，你可以使用两个反斜杠，这样它才会保持它原本的含义。例如：

```java
logger.debug("File name is C:\\\\{}.","file.zip");
```
将会打印“File name is C:\file.zip”。

-
####4.如何打印单独（可能是复杂的）对象的字符串内容日志？
需要以字符串格式记录对象的消息，这是相对少见的格式，那么可以使用适当级别的参数化打印方法。假设complexObject是一个已确定复杂度的对象，对于一个DEBUG级别的日子语句。你可以这样写：

```java
logger.debug("{}",complexObject);
```
在日志系统已查明日志系统已开启后，它将调用complexObject.toString()方法。否则，complexObject.toString()转换的开销将有力地避免。

-
####5.为什么org.slf4j.Logger接口没有FATAL级别的方法？
[标记](http://www.slf4j.org/apidocs/org/slf4j/Marker.html)接口，是org.slf4j包的一部分，指出FATAL级别很大程度上是多余的。如果一个给定的错误需要的注意超过了分配给普通错误的注意，使用特殊指定的标记来简单标记日志语句就可以命名“FATAL”或其他你喜欢的名字.
例如：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.Marker;
import org.slf4j.MarkerFactory;
class Bar{
 void foo(){
   Marker fatal =MarkerFactory.getMarker("FATAL");
   Loggerlogger=LoggerFactory.getLogger("aLogger");
   try{
     ... obtain a JDBC connection
   }catch(JDBException e){
     logger.error(fatal,"Failed to obtain JDBC connection", e);
   }
 }
}

```
虽然标记是SLF4J API的一部分，只有l​​ogback支持现成的标记。例如，如果你添加%marker转换字到它的样式中，logback的PatterenLayout将添加标记数据到它的输出口。标记数据可以用于[过滤消息](http://logback.qos.ch/manual/filters.html)，或者甚至在[单个事务结束后](http://logback.qos.ch/recipes/emailPerTransaction.html)[发出](http://logback.qos.ch/manual/appenders.html#OnMarkerEvaluator)电子邮件。
在日志框架的组合体中，例如log4j和java.util.logging，是不支持标记的。标记数据将被默默地忽略掉。

相比于五个值，即错误、警告、信息,调试和跟踪，这是所允许的日志级别，为了处理日志语句，标记增加了一个新的具有无限可能值的维度。目前。只有logback支持标记数据。然而，没有什么阻止其他日志框架使用标记数据。

-
####6.为什么TRACED级别的日志只在SLF4J 1.4.0版本介绍？

添加TRACED级别已成为频繁和激烈讨论的要求。通过研究各种项目，我们得出TRACE级别被用于某些类禁用日志输出，而不需要为这些类配置日志。实际上，在log4j和logback里，TRACE级别默认是禁止，这在大部分其他日志系统中也是禁止的。在配置文件里增加合适的指令，相同的结果也可以获得到。

因此，在许多情况下，TRACE级别像DEBUG级别一样带有相同的语意含义。在这些情况下，TRACE级别只是保存一些配置指令。在其他更有趣的场合，TRACE级别相比于DEBUG级别带有不同的含义，[Marker](http://://www.slf4j.org/api/org/slf4j/Marker.html)对象可以用于传递所需的含义。然而，如果用标记你不被打扰，希望使用比DEBUG级别低的日志级别，那么TRACE级别可以完成这个任务。

注意，虽然评估禁用日志请求的开销是在几纳秒内。在密集的循环中，使用TRACE级别（或此种事务下的任何其他日志级别）是不鼓励的，这里日志请求可能被计算数百万次。如果日志请求是开启的，那么它将用大量输出来淹没目标位置。如果请求被禁用，它将浪费资源。

简言之，尽管我们仍不鼓励使用TRACE级别日志，因为存在可选择的日志级别，或者在许多情况里，TRACE级别的日志请求是浪费的，由于人们一直在问它，我们决定屈服于大众的需求。

-

####7.SLF4J日志API支持I18N（国际化）吗？

是的，作为的1.5.9版本，SLF4J附带一个包叫做org.slf4j.cal10n，它增加了[本地化/国际化日志记录](http://www.slf4j.org/localization.html)的支持，在内置薄薄的一层[CAL10N API](http://cal10n.qos.ch/) .

-

####8.不通过LoggerFactory里的静态方法，可以重新获取日志器吗？

是的， LoggerFactory本质上是一个围绕[ILoggerFactory](http://www.slf4j.org/xref/org/slf4j/ILoggerFactory.html)实例的包装。使用ILoggerFactory实例是由SLF4J底层的静态绑定约定确定的。查看LoggerFactory的[getSingletong()](http://www.slf4j.org/xref/org/slf4j/LoggerFactory.html#217)方法获取详情 .

然而，没有什么能阻止你使用自己的ILoggerFactory实例。注意，通过调用[LoggerFactory.getILoggerFactory()](http://www.slf4j.org/apidocs/org/slf4j/LoggerFactory.html#getILoggerFactory())方法，你也可以获得LoggerFactory类使用的ILoggerFactoyr的一个引用。

因此，如果SLF4J绑定约定不符合你的需求，或者你需要额外的拓展性，那么请考虑使用ILoggerFactoyrj接口，作为你自己创造的日志API的一个可选择接口.

-
####9.存在错误/抛出异常时，可以参数化日志声明吗？

是的，像SLF4J1.6.0版本一样，但这之前的版本里不是这样。在异常出现的地方，SLF4J API支持参数化，假设异常时最后的参数吗。因此，

```java
String s = "Hello world";
try {
  Integer i = Integer.valueOf(s);
} catch (NumberFormatException e) {
  logger.error("Failed to format {}", s, e);
 }
```
如期望的一样，将会打印NumberFormatException，同时也包括它的栈追踪信息。Java编译器将调用传入[一个字符串和两个对象变量的error方法](http://www.slf4j.org/apidocs/org/slf4j/Logger.html) 。SLF4J按照程序猿的最大可能意图，将像一个抛出异常一样解释NumberFOrmatException实例，而不是简单的忽略掉。

如果异常不是最后的变量，它将被认为是一个平常的对象，它的栈信息将不会被打印。当然，这种情况在实际操作中应该不会发生。

-
##实现SLF4J API
####1.如何使我的日志底层框架SLF4J有可兼容性？

为SLF4J添加支持是So Easy的一件事。本质上，你复制一个已存在的绑定，裁剪绑定的一点点就完成这个小伎俩（如下解释）。

假设你的日志系统有日志器的概念，称为MyLogger，你需要为MyLogger到org.slf4.Logger接口提供一个适配器。参考slf4j，slf4j-jdk14,和slf4j-log4j12模块中适配器的例子。

一旦你写完一个合适的适配器，叫MyLoggerAdapter，你需要提供一个工厂类，它实现org.slf4j.IloggerFactory接口。这个工厂应该返回MyLoggerAdater的实例。使MyLoggerFactoyr是你工厂类的名字。

一旦有了名为MyLoggerAdater的适配器，和一个名为MyLoggerFactoyr的工厂类，最后剩下的步骤就是改变StaticLoggerBinder类，这样它会返回一个MyLoggerFactory新实例。你也需要修改loggerFactoryClassStr变量。

对于Marker(标记)或MDC支持，你可以使用已有的NOP（slf4j.nop.jar是sl4f-api.jar其相应的接口实现）实现中的一个。

综上，为你的日志系统创建一个SLF4J绑定，步骤如下：

	1.开始复制一个已有的模块，
	2.创建一个基于你日志系统和slf4j.Logger接口间的适配器
	3.为上一步骤里的适配器创建一个工厂类
	4.修改StatciLoggerBinder类，以使用上一步骤创建的工厂类。
	
####2.如何使我的日志系统添加支持Marker接口？

标记设立了一个革命性的概念，这是由logback支持的，但其他已有日志系统是不支持这个的。所以，SLF4J相容日志系统允许忽略用户通过标记数据。

然而，即使标记数据可能被忽略，用户仍必须被允许指定标记数据。否则，用户将不能在支持标记的日志系统和不支持标记的日志系统间切换。

MakrerIgnoringBase类可作为适配器或缺少标志支持的日志系统的本地实现的基类。在MarkerIgnoringBase类里，带有标记数据的方法简单地调用没有标记参数、丢弃作为参数传入的任何标记数据的相关方法，你的SLF4J适配器可以继承MakrerIgnoringBase类来快速实现org.slf4j.Logger里的方法，org.slf4j.Logger需要标记作为第一个参数。

####3.SLF4J的版本检查机制如何工作？

SLF4J初始化期间，它执行的版本检查是可选过程。相容的SLF4J实现可选择不不参加，在这种情况下，将不会执行版本检查。

然而，如果SLF4J实现决定参与，那么它需要声明一个叫REQUESTED_API_VERSION的变量，同时得在StaticLoggerBinder类的拷贝里声明。次变量的值应该等于slf4j-api编译的版本。如果实现被更新到新的slf4j-api版本，那么你也需要更新REQUESTED_API_VERSION的值。

对每一个版本，SLF4J API都保持兼容版本的列表。如果请求的版本在兼容列表中没被找到，SLF4J将发出一个单本不匹配警告。即使SLF4J绑定比SLF4J有一个不同的发布，假设每6到12个月，你更新SLF4J版本，你仍可以在没触发版本不匹配的情况下，参与版本检查。例如，logback又一个不同的发布日程，但是仍然会参与版本检查。

对于SLF4J 1.5.5,所有绑定在SLF4J分布，例如slf4j-log4j12，slf4j-simple和slf4j-jdk14里传递,声明REQUESTED_API_VERSION，它的值等于它们的SLF4J版本。它遵循的是，例如如果slf4j-simple-1.5.8与 slf4k-api-1.6.0,jar混合使用，基于1.5.8不在SLF4J版本1.6.x的兼容列表，将会触发版本不匹配警告。

注意，1.5.5之前的SLF4J版本没有版本检查机制。只有slf4j-api-1.5.5.jar以后的版本可触发版本不匹配警告。

-
##关于日志的一般性问题

####1.类的日志器成员变量应该声明为静态变量吗？

我们通常推荐的是loggers成员声明为实例变量，而不是静态变量。进一步分析后，在其他方法上，我们不再推荐一种方法。

下面是每一个方法的有点和缺点。

| 声明logger为静态变量的优点    | 声明logger为静态变量的缺点|
| ------------- |:-------------:| 
| 1.   通用、很好建立的惯用语句；2.   更小的CPU开销：在主机类初始化过程中，loggers获取和分配只要一次；3.   更小的内存开销：logger声明每个类消耗一个引用     | 1.在应用间共享库，不能发挥仓库选择器的优势。应该注意，如果SLF4J绑定和底层API附带每一个应用（不在应用间分享），那么每个应用仍将有自己的日志环境。2.IOC不友善 |

| 声明loggers为实例变量的优势  | 声明loggers为实例变量的缺点     |
| ------------- |:-------------:| 
| 1.   可发挥仓库选择器的优势，甚至应用间的共享库。然而，只有底层日志系统是logback-classic的，仓库选择器才起作用。SLF4J+log4j相结合的方式，仓库选择器将不起作用。2.   IOC友好 | 1.   比声明loggers为静态变量有更少的惯用语句2.   更高的CPU开销：loggers为主机类的每个实例进行获取和分配3.   更高的内存开销：logger声明主机类的每个实例都消耗一个引用| 
**说明**

静态日志成员为类的所有实例花费一个单独的变量引用，而实例日志成员将为类的每个实例花费一个变量引用。对简单的类进行数以千计次的初始化可能出现一次明显的区别。

然而，最近的日志系统，例如log4j或logback,为每个运行在应用服务器上的应用提供一个不同的logger上下文。因此，即使一份log4j.jar或logback-classic.jar的单拷贝有效地运用在服务器上，日志系统能够在应用间进行区分，同时为每个应用提供一个不同的日志环境。

更特别的是，每次，通过调用LoggerFactory.getLogger()方法可以重新获得logger,底层日志系统将给当前应用返回适当的实例。请注意，在相同的应用里获取一个给定名字的logger通常会返回相同的logger.对于给定的名字，对不同的应用将返回不同的logger.

如果logger是静态的，当主机类已加载到内存时，那么它将只被获得一次。如果主机类只在一个应用里使用，那么这里没有太多需要关心的地方。然而，如果主机类在几个应用间共享，那么所有共享类的实例将记录到应用的上下文里，这都发生在第一次加载共享类到内存里的时候-几乎是不被用户期望的行为。

不幸的是，对于SLF4J API的非本地实现，名字为slf4j-log4j12,log4j的仓库选择器将不能正常地完成它的工作，因为slf4j-log4j12,是非本地SLF4J绑定，将在字典里存储logger,短路循环依赖logger上下文重新获取。对于本地SLF4J实现方法，例如logback-classic,仓库选择器将如期工作。

Apache Commons wiki有一篇覆盖这个问题的[文章](http://wiki.apache.org/commons/Logging/StaticLog)。

**Logger序列化**

与静态变量相反，实例变量默认是序列化的。对于SLF4J版本1.5.3，

Logger实例变量幸好是初始化的。因此，主机类的初始化不再需要任何特别的指令，即使当loggers被声明为实例变量。在之前的版本里，logger实例需要在主机类里声明为transient类型变量。

总结

综上，声明logger成员为静态变量需要更少的CPU时间，有一个更小的内存开销。另一方面，声明logger成员为实例变量需要更多的CPU时间和更高的内存开销。然而，实例变量使得为每一个应用构建一个不同的logger环境，甚至为共享库里声明的logger成为可能。可能，比之前涉及的因素更重要的是，实例变量是IOC友好的，而静态变量却不是。

查看commons-logging wiki里相关[讨论](http://wiki.apache.org/commons/Logging/StaticLog)。

-
####2.类里的声明日志器有推荐的习语吗？

以下是推荐的记录声明成语。原因如上所述，这是留给用户确定记录器是否被声明为静态变量或没有。

```java
package some.package;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
public class MyClass {
   final (static) Logger logger = LoggerFactory.getLogger(MyClass.class);
   ... etc
}
```
不幸的是，由于主机类的名称是记录器声明的一部分，上面logger声明习语是不能在类之间抵抗剪切和粘贴。
