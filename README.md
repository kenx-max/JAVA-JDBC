## 日志等级
![图片](https://images.soboys.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTE5MDE0NTU1Mjk3.png)

图片上是比较常用的日志等级实际上有8个级别（除去OFF和ALL，可以说分为6个级别），优先级从高到低依次为：**OFF、FATAL、ERROR、WARN、INFO、DEBUG、TRACE、 ALL**
1. **ALL** 最低等级的，用于打开所有日志记录。
2. **TRACE** designates finer-grained informational events than the DEBUG.Since:1.2.12，很低的日志级别，一般不会使用。
3. **DEBUG** 指出细粒度信息事件对调试应用程序是非常有帮助的，主要用于开发过程中打印一些运行信息。
4. **INFO** 消息在粗粒度级别上突出强调应用程序的运行过程。打印一些你感兴趣的或者重要的信息，这个可以用于生产环境中输出程序运行的一些重要信息，但是不能滥用，避免打印过多的日志。
5. **WARN** 表明会出现潜在错误的情形，有些信息不是错误信息，但是也要给程序员的一些提示。
6. **ERROR** 指出虽然发生错误事件，但仍然不影响系统的继续运行。打印错误和异常信息，如果不想输出太多的日志，可以使用这个级别。
7. **FATAL** 指出每个严重的错误事件将会导致应用程序的退出。这个级别比较高了。重大错误，这种级别你可以直接停止程序了。
8. **OFF** 最高等级的，用于关闭所有日志记录。

如果将**log level**设置在某一个级别上，那么比此级别优先级**高的log**都能打印出来。例如，如果设置优先级为WARN，那么OFF、FATAL、ERROR、WARN 4个级别的log能正常输出，而INFO、DEBUG、TRACE、 ALL级别的log则会被忽略。Log4j建议只使用四个级别，优先级从高到低分别是ERROR、WARN、INFO、DEBUG。
## logback配置
在项目中使用**logback**时，它会默认在项目的**classpath**路径下按顺序查找名为**logback-test.xml、logback.groovy、logback.xml**的文件，如果上述文件均未找到，则使用**默认配置（debug级别）将日志输出到控制台**。

可以通过Printing Logger Status来知道logback内部状态

```java
class logBack1 {
    private final static Logger logger = LoggerFactory.getLogger(LogBack.class);

    public static void main(String[] args) {
        // 打印内部的状态
        LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
        StatusPrinter.print(lc);
    }
}
```
运行内容如下：
```text
13:06:09,042 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback-test.xml]
13:06:09,043 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.groovy]
13:06:09,043 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.xml]
13:06:09,048 |-INFO in ch.qos.logback.classic.BasicConfigurator@7daf6ecc - Setting up default configuration.

```

在没有对logback进行配置的情况下，可以进行简单的日志输出，代码如下：
```java
package cn.soboys.logback;


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LogBack {
    private final static Logger logger = LoggerFactory.getLogger(LogBack.class);

    public static void main(String[] args) {
        logger.error("logback error测试");
        logger.warn("logback warn 测试");
        logger.info("logback info测试");
        logger.debug("logback debug测试");
        logger.trace("logback trace测试");
    }
}

```
运行上面代码，可以在控制台有以下信息输出（默认输出格式为 %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n）：
```text
12:45:35.889 [main] ERROR cn.soboys.logback.LogBack - logback error测试
12:45:35.892 [main] WARN cn.soboys.logback.LogBack - logback warn 测试
12:45:35.892 [main] INFO cn.soboys.logback.LogBack - logback info测试
12:45:35.892 [main] DEBUG cn.soboys.logback.LogBack - logback debug测试
```





