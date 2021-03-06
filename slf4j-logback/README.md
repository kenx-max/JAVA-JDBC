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
可以看到，在项目没有对logback进行配置时，logback可以使用默认配置输出简单的日志到控制台，但在实际工作中，
我们通常对项目日志有更为严格的要求，比如将日志按日期每天产生日志文件、将日志按文件大小进行分割、定期删除日志等。因此需要在项目中建立logback的配置文件，当在项目的classpath路径下存在logback.xml（或者logback-test.xml、logback.groovy）,logback能够自动扫描到它并读取配置，

下面是一个最基本的logback配置文件。
```xml
<configuration>
    <!--指定日志输出位置-->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!-- encoders are assigned the type
             ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!--指定日志级别-->
    <root level="debug">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```
### configuration
configuration作为logback配置文件的根节点，有**scan、sacnPeriod、debug**等属性。

1. **debug**: 当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。
(为true时和上面在logBack1的main方法加入的两行代码效果相同)
2. **scan**: 当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。
3. **scanPeriod**: 设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是**毫秒**。当scan为true时，此属性生效。默认的时间间隔为1分钟。
4. **packagingData**：当此属性设置为true时，logback可以包含它输出的堆栈跟踪行的每一行的打包数据。打包数据由jar文件的名称和版本组成，而这个jar文件是由堆栈跟踪线的类产生的。默认值为false。它也可以通过java方式启用：
综合上面所述，configuration的配置如下：
```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false" packagingData="false">
</configuration>
```
### appender
指定日志输出的位置它接受两个强制性的属性（name和class）。name属性指定appender的名称，而class属性指定要实例化的appender类的完全限定名
常用的appender的有**ConsoleAppender，FileAppender，RollingFileAppender**
#### ConsoleAppender
顾名思义，在控制台上追加。示例：
```xml
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
#### FileAppender
FileAppender，一个OutputStreamAppender的子类，将日志事件附加到一个文件中。目标文件由文件选项指定。如果该文件已经存在，
它将根据附加属性的值被追加或截断。示例
```xml
<configuration>
    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <!--日志文件名
          路径：当前项目根目录没有就新建
        -->
        <file>testFile.log</file>
        <!--写入方式
            true：追加写入
            false：清空写如
        -->
        <append>true</append>
        <!-- 设置为false可获得更高的日志记录吞吐量 -->
        <immediateFlush>true</immediateFlush>
        
        <!-- encoders are assigned the type
             ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
        <encoder>
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="DEBUG">
        <appender-ref ref="FILE"/>
    </root>
</configuration>
```
在应用程序开发阶段或短期应用程序(例如批处理应用程序)期间，希望在每个新应用程序启动时创建一个新的日志文件。这在**timestamp**元素的帮助下很容易做到。示例：
```xml
<configuration>

  <!-- 定义一个时间戳 属性值为后续配置文件使用-->
  <timestamp key="bySecond" datePattern="yyyy-MM-dd HH:mm:ss"/>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <!--使用先前创建的时间戳创建唯一
                  命名日志文件-->
    <file>log-${bySecond}.txt</file>
    <encoder>
      <pattern>%logger{35} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```
#### RollingFileAppender
RollingFileAppender将FileAppender扩展为可**翻转日志文件的功能**。例如，RollingFileAppender可以记录日志到一个名为log.txt的文件，一旦满足一定条件，
将其日志目标更改为另一个文件。在使用时，RollingFileAppender必须同时具有RollingPolicy和TriggeringPolicy设置。
但是，如果它的RollingPolicy也实现TriggeringPolicy接口，那么只需要显式地指定前者。

**TimeBasedRollingPolicy**
时间基准滚动策略可能是最流行的滚动策略。它定义了一个基于时间的滚动策略，例如逐日或逐月。时间的滚动策略承担了翻转的责任，同时也承担了触发的滚动。事实上,TimeBasedTriggeringPolicy实现RollingPolicy和TriggeringPolicy接口。
```xml
<configuration>
  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>logFile.log</file>
    <!--指定日志翻转触发条件
        TimeBasedRollingPolicy 基于时间触发
     -->
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- 滚动策略
            每月
      -->
      <fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern>

      <!-- 保留30天的历史记录 -->
      <maxHistory>30</maxHistory>
       <!--上限为3GB--> 
      <totalSizeCap>3GB</totalSizeCap>

    </rollingPolicy>

    <encoder>
       <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender> 

  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```
**SizeAndTimeBasedRollingPolicy**
有时，可能希望按日期对文件进行存档，但同时限制每个日志文件的大小，这时候可以使用SizeAndTimeBasedRollingPolicy达到目的
```xml
<configuration>
  <appender name="ROLLING" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>mylog.txt</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
      <!-- rollover daily -->
      <fileNamePattern>mylog-%d{yyyy-MM-dd}.%i.txt</fileNamePattern>
       <!-- 每个文件最多100MB，保留60天的历史记录，但最多20GB -->
       <maxFileSize>100MB</maxFileSize>    
       <maxHistory>60</maxHistory>
       <totalSizeCap>20GB</totalSizeCap>
    </rollingPolicy>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>


  <root level="DEBUG">
    <appender-ref ref="ROLLING" />
  </root>

</configuration>

```












