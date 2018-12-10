## 如何省略类文件开头创建Logger对象的代码行？
> By SunnyChan (sunnnychan@gmail.com)  
> 2018/10/25

### 问题背景
```md
从我接触Java开始，经常类中有一行类似的代码：
```
```java
 private static final Logger log = LoggerFactory.getLogger(ClassName.class);
```
```md
怎么看它怎么不爽，为什么我们需要写这种约定俗成的代码？
实际上我们一直在想办法（AOP、注解等），将打印日志、埋点之类非业务类代码与业务代码隔离开。

而且我仍清晰地记得，当初刚开始写Java代码的时候，由于对Logger的机制了解不够，
因为不知道为什么需要传 类名 参数，所以擅作主张的把上面的代码写成了：
```
```java
 private static final Logger log = LoggerFactory.getLogger();
```
```md
一开始代码运行没有任何问题，过了没多久就发生了内存泄漏。
刚开始还有点懵，通过 分析工具发现 栈中存在大量的 Logger 对象。

深入之下，才发现 logback 有一个缓存机制，缓存的 Key 就是类名，
相当于每一个类文件共有一个 Logger 对象，这样 Logger 对象 的数量是可控的。

当我不使用类名做参数时，那就是 每执行一次上述的代码就会创建一个 Logger 对象，
而在业务代码中，打印日志是多么频繁的一个操作，所以如果并发量高了之后就可想而知了。

这是我开始写Java程序给自己埋的第一个大坑，至今印象深刻。
```
### 日志功能扩展
```
Logger 打印日志方式有些简单，如果需要打印一些固定的信息，
而这些信息又是不能通过Logger的日志格式化串配置的。
例如：业务日志中经常使用的 traceId。
当然我们需要实现的可扩展些。

另外，有时我们还需要对日志的打印做一些控制。
```

### 实现功能列表
```md
总结我们需要实现的日志功能：
1. 实现怎么去掉这行代码。直接在代码中按照如下方式打印：
   Log.error("this is a log line");
2. 固定的扩展信息打印（traceId等）
3. 代码行信息打印
4. 单条日志长度控制
5. 业务扩展日志通用信息（用户自定义）
6. 动态配置日志文件
   对应于需要将日志打印到不同的文件的场景，如不同的Worker线程处理不同的流程
   需要打印日志到不同的文件，做日志的隔离。
   该功能需要配置 logback 的 MDC 功能来实现，具体的配置这里就不讲了。
```

### 实现思路
```md
首先 定义以下数据结构：
```
```java
public final class Log {
  // 相对于日志类，业务代码调用的层级
  private static Integer bizCallLayer = 0;
  // 最大单行日志打印的长度，有默认值，也可以初始化
  private static Integer logMessageLength = 5000;
  // 固定的扩展日志存储结构
  private static TransmittableThreadLocal<HashMap<String, String>> logCommonMsg
      = new TransmittableThreadLocal<HashMap<String, String>>();
  // 业务扩展日志通用信息，由用户自定义，并初始化，也可以不使用
  private static TransmittableThreadLocal<String> bizLogCommonMsg
      = new TransmittableThreadLocal<>();
  // 动态配置日志文件时，输出的日志文件名
  private static TransmittableThreadLocal<String> mdcLogfile
      = new TransmittableThreadLocal<>();
}
```

#### bizCallLayer
```md
bizCallLayer 只有在你对 Log 类又做了封装的时候才会发生作用。
因为我们需要打印业务类中代码的行号等信息，以便于出现异常时定位问题，
如果在Log的基础上再做几层封装，由于不知道业务层代码从哪里开始，上述的信息也就无法获取了。

所以就需要初始化这个信息，如果封装了n，那 bizCallLayer 就需要初始化为n。
```
#### logMessageLength
```
logMessageLength 是用来控制日志行长度的，太长的日志，容易影响性能，有时候也没有什么必要。
因为有这个机制在，所以 重要的日志信息要尽量放在前面打印，或者单独做一行打印。

注意 ：bizCallLayer 和 logMessageLength 由于是 static 修饰的变量，所以修改的话，是影响全部线程的。
```
#### TransmittableThreadLocal
```md
为什么使用 TransmittableThreadLocal 对象？
首先，我们肯定需要使用 ThreadLocal 来保存 线程的日志信息，这样才能保证不会发生打印错乱。

但是有一个场景是 ThreadLocal 不能满足的：
如果一个线程又创建一个子线程来处理子任务，子线程是无法获取到父线程的相关日志信息的，
这有时候是有必要的，如从业务场景来看，这是一个请求的处理过程，应该有统一的 traceId。

TransmittableThreadLocal 是阿里开源的一个线程同步结构，它可以继承父线程同步结构空间的数据。
这和我们的需求完全契合，完美的解决了我们的困扰。
```

#### Thread.currentThread().getStackTrace()
```md
线程的调用栈信息，里面的信息很丰富，可以打印任何对你有益的信息。代码行信息打印 的需求，也是从这里获取。

bizCallLayer 可以帮助你区分业务层代码的调用是从哪一层开始的。
```

#### Log.error()
```md
Logger 中提供的 trace、debug、info、warn、error级别的日志打印分发我们都需要重新封装。
这里我们简单实现一个：
```
```java
  public static void debug(String format, Object... value) {
    Logger logger = LoggerFactory.getLogger(Log.getLoggerName());
    if (!Objects.isNull(Log.mdcLogfile.get())) {
      MDC.put("logFileName", Log.mdcLogfile.get());
      logger.debug(Log.getLogMessage(String.format(format, value)));
      MDC.remove("logFileName");
    } else {
      logger.debug(Log.getLogMessage(String.format(format, value)));
    }
  }
```
```md
你可能发现了 Logger 对象的创建被移动了这里，参数变成了一个方法调用。
如果是动态的日志文件，还需要先设置日志文件路径，然后再打印。

Log.getLoggerName() 方法就是从线程调用栈中获取 业务类名。
```
```java
  private static String getLoggerName() {
    StackTraceElement stack = Thread.currentThread().getStackTrace()[3 + bizCallLayer];
    return stack.getClassName();
  }
```
```md
Log.getLogMessage() 实现获取要打印的日志内容。
主要是两部分内容，固定的日志信息 和 传入的 日志信息参数。
```
```java
  private static String getLogMessage(String logMsg) {
    StringBuilder logMsgBuilder = new StringBuilder(500);
    logMsgBuilder.append(" [");
    logMsgBuilder.append(Thread.currentThread().getName());
    logMsgBuilder.append("] ");
    StackTraceElement stack = Thread.currentThread().getStackTrace()[4 + bizCallLayer];
    logMsgBuilder.append(stack.getFileName());
    logMsgBuilder.append(":");
    logMsgBuilder.append(stack.getLineNumber());
    if (!Objects.isNull(logCommonMsg.get())) {
      logMsgBuilder.append("xxx");
    }
    if (!Objects.isNull(extendLogCommonMsg.get()) {
      logMsgBuilder.append("xxx");
    }
    String logMessage = logMsgBuilder.append(logMsg).toString();
    return (!Objects.isNull(Log.logMessageLength) && Log.logMessageLength > 0
        && logMessage.length() > Log.logMessageLength)
        ? logMessage.substring(0, Log.logMessageLength - 1)
        + " ... ... HERE OMITTED MANY WORDS ... ..." : logMessage;
  }
```
```md
以上分别是打印 线程名、调用的业务类文件名、代码行号、固定日志信息、业务扩展日志信息、以及本次的日志信息参数。
最后 对日志的长度做一个控制。

以上我们就基本实现了文章开头提出的一些需求。
```

