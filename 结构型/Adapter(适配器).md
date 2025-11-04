**Adapter 适配器——类对象结构模式**

**类型**：结构型

这个设计模式**在日常开发中出镜率极高**，关键还不难，所以务必要掌握。

**定义：**将一个接口转换为客户端所期待的接口，从而使两个接口不兼容的类可以在一起工作。

适配器模式还有个别名叫：Wrapper（包装器），顾名思义就是将目标类用一个新类包装一下，相当于在客户端与目标类直接加了一层。IT世界有句俗语：没有什么问题是加一层不能解决的

**使用场景**

1.  当需要使用一个现存的类，但它提供的接口与我们系统的接口不兼容，而我们还不能修改它时
2.  当多个团队独立开发系统的各功能模块，然后组合在一起，但由于某些原因事先不能确定接口时。（**别和我说这不可能，这太tm可能了**）

**UML类图**

**参与者**

|     |     |
| --- | --- |
| Target | 定义Client使用的与特点领域相关的接口 |
| Client | 与符合Target接口的对象协同 |
| Adaptee | 定义一个已经存在的接口(也可以是类)，这个接口需要适配 |
| Adapter | 对Adaptee的接口与Target接口进行适配，它需要实现目标接口Target，而且必须引用Adaptee,因为我们要在此类中包装Adaptee的功能。 |

**实例**

你开发了一个非常棒的改进项目的log系统，但是此开源库与项目中的项目完全不兼容，所以你做了如下的改变：

**第一步：确定目标接口**

系统原来的日志接口如下

public interface LogFactory {

void debug(String tag,String message);

}

**第二步：三方库接口及实现**

下面是第三方库提供的日志功能，但是其接口与原系统目前使用的不兼容。

public interface [NbLogger](https://zhida.zhihu.com/search?content_id=170190024&content_type=Article&match_order=1&q=NbLogger&zhida_source=entity) {

void d(int priority, String message, Object ... obj);

}

//具体提供日志功能的实现类

public class NbLoggerImp implements NbLogger {

@Override

public void d(int priority, String message, Object... obj) {

System.out.println(String.format("牛逼logger记录:%s",message));

}

}

**第三步：构建适配器类**

这个类是适配器模式的核心，通过此类就可以将三方库提供的接口转换为系统的目标接口

public class LogAdapter implements LogFactory {

private NbLogger nbLogger;

public LogAdapter(NbLogger nbLogger) {

this.nbLogger = nbLogger;

}

@Override **//这里使用可重写，主要是为了方便新的方案接入后，重写适配的方法**

public void debug(String tag, String message) {

Objects.requireNonNull(nbLogger);

nbLogger.d(1, message);

}

}

LogAdapter实现了系统的目标接口，同时持有三方库NbLogger的引用。

**第四步：客户端使用**

public class AdapterClient {

public void recordLog() {

LogFactory logFactory = new LogAdapter(new NbLoggerImp());

logFactory.debug("Test", "我将使用牛逼logger打印log");

}

}

可以看到，通过适配器客户端就可以很轻松的切换到新的日志系统了。

**技术要点总结**

适配器LogAdapter 必须要实现目标接口，且依赖那个提供功能的类型，此处为NbLogger

**优点**

极大的增强了程序的可扩展性，通过此模式，你可以随意扩展程序的功能，但却不需要修改接口

**总结**

其实你也看得明白就是通过 **_组合_** 行为实现的，最后将执行逻辑的方向进行重写，实现方法的转换(**_实现“接口转换”的目的_**)。