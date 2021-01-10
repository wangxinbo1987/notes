### 关于Java日志组件的选择

#### 历史

**log4j**是Java日志框架的元老，1999年发布首个版本，2012年发布最后一个版本，2015年正式宣布终止，至今还有无数的系统在使用log4j，甚至很多新系统的日志框架选型仍在选择log4j

尽管log4j有着出色的历史战绩，但早已不是Java日志框架的最优选择

在log4j被Apache Foundation收入门下之后，由于理念不合，log4j的作者Ceki离开并开发了**slf4j**和**logback**

slf4j因其优秀的性能和理念很快受到了广泛欢迎，2016年的统计显示，github上的热门Java项目中，slf4j是使用率第二名的类库（第一名是junit）

logback则吸取了log4j的经验，实现了很多强大的新功能，再加上它和slf4j能够无缝集成，也受到了欢迎

在这期间，Apache Logging则一直在关门憋大招，log4j2在beta版鼓捣了几年，终于在2014年发布了GA版，不仅吸收了logback的先进功能，更通过优秀的锁机制、LMAX Disruptor、"无垃圾"机制等先进特性，在性能上全面超越了log4j和logback

---

阿里巴巴Java开发手册中规定

> **[强制]** 应用中不可直接使用日志系统（Log4j、Logback）中的API，而应依赖使用日志框架（SLF4J、JCL--Jakarta Commons Logging）中的API，使用门面模式的日志框架，有利于维护和各个类的日志处理方式统一

#### slf4j (Simple Logging Facade for JAVA)

slf4j是一个“日志门面”（Logging Facade），而不是一个完整的日志框架。它提供了一套记录日志的api，但不提供输出日志的功能，而是通过对接如log4j，logback，java.util.logging等日志框架，来实现日志的输出

slf4j与JCL的主要区别在于日志服务的**绑定机制**

JCL采用的**动态绑定机制**

1. 在进程启动时尝试获取名为"org.apache.commons.logging.Log"的配置属性（可与在commons-logging.properties文件中配置，或使用Java代码进行配置），按配置选取对应的日志输出服务
1. 如果没有获取到对应配置属性，会尝试在系统参数中寻找名为"org.apache.commons.logging.Log"的参数项
1. 如果1,2均没有获取到，会在classpath下寻找log4j的相关class，如果找到，则使用log4j作为日志输出服务
1. 如果没有找到log4j，则尝试使用java.util.logging包作为日志输出服务
1. 如果上述都失败，则使用SimpleLog作为日志输出服务，即将所有日志输出至控制台标准输出System.err

JCL的动态绑定机制基于ClassLoader实现，有以下缺陷

- 效率较低
- 容易引发混乱，在一个复杂甚至混乱的依赖环境下，确定当前正在生效的日志服务是很费力的，特别是在程序开发和设计人员并不理解JCL的机制时
- 最致命的问题：在使用了自定义ClassLoader的程序中，使用JCL会引发各类问题，例如内存泄露、与OSGI冲突等。

而slf4j则简单得多，采用**静态绑定机制**

1. slf4j为各类日志输出服务提供了适配库，如slf4j-log4j12，slf4j-simple，slf4j-jdk14等。一个Java工程下只能引入一个slf4j适配库
1. slf4j会加载org.slf4j.impl.StaticLoggerBinder作为输出日志的实现类。这个类在每个适配库中都存在，所以slf4j不需要像JCL一样主动去寻找日志输出实现，自然而然地就能与具体的日志输出实现绑定起来
1. 当需要更换日志输出服务时（比如从logback切换回log4j），只需要替换掉适配库即可

所以slf4j对比JCL不仅有性能上的优势，使用slf4j的程序员也不需要去翻找配置文件或追踪启动过程就能够清除明白地了解当前使用的是什么日志输出服务。

---

#### slf4j提供的适配库和桥接库

适配库：

- slf4j-log4j12：使用log4j-1.2作为日志输出服务
- slf4j-jdk14：使用java.util.logging作为日志输出服务
- slf4j-jcl：使用JCL作为日志输出服务
- slf4j-simple：日志输出至System.err
- slf4j-nop：不输出日志
- log4j-slf4j-impl：使用log4j2作为日志输出服务
- logback天然与slf4j适配，不需要额外引入适配库（同一个作者）

桥接库：

- log4j-over-slf4j：将使用log4j api输出的日志桥接至slf4j
- jcl-over-slf4j：将使用JCL api输出的日志桥接至slf4j
- jul-to-slf4j：将使用java.util.logging输出的日志桥接至slf4j
- log4j-to-slf4j：将使用log4j2输出的日志桥接至slf4j


[看完了，回到目录](/README.md)