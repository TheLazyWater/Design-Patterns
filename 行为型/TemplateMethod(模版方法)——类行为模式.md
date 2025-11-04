**Template Method Pattern**

**类型**：行为型

**定义**：在一个方法中定义一个算法的骨架，而将一些步骤的实现延迟到子类中，使得子类可以在不改变一个算法的结构前提下即可重定义该算法的某些特定步骤

**使用场景**

这个设计模式一般在最初写代码的时候基本上是不会预先想到的，都是在后期不断重构的过程中被提炼出来的。简单来说就是：当你发现你写了两个或者多个几乎一毛一样的类，只是有些方法的实现方式不一致的时候，你就要停下来想想了，如果你又发现在使用这些类的地方调用方法顺序都一样，不要犹豫请使用模板方法模式重构吧。

UML图

如上图所示，模板方法模式关键点如下：一个模板类LivePlay,里面有一个定义为final的模板方法seeLivePlay(). 多个模板实现类。

**具体业务场景**

直播行业是2017年互联网行业的风口，有熊猫直播、映客直播、陌陌直播、虎牙直播。。。说是全民直播也不为过。王二狗他们老板也想当风口的猪，就是说它想借着风上天，于是也加入了直播行业大军。小公司不可能自己开发一套直播的基础架构系统，实力不允许，于是就想到了接入第三方的直播SDK，刚开始接入了腾讯的直播SDK，后来由于不稳定又要同时接入金山的SDK，而且要做到可以互相切换。 当王二狗带着小弟林蛋大日夜赶工，一个月后把这个功能实现后发现，你妈这重复代码太多了，的重构啊，不然老板哪天又要接其他家的SDK可咋办，总不能把几乎一毛一样的代码再写一遍吧。 因为用户看直播是有一套固定的模板流程的：**登录---进入房间---获取音视频流---观看---停止音视频流---退出房间** 虽然两套SDK每一个步骤的实现方式不同，但是基本都是遵循同一套流程。王二狗猛然发现，这不就是给模板方法模式准备的场景嘛，于是乎撸起袖子就干了！

**具体实现**

**第一步**：先定义一个模板类LivePlay,如下代码片段所示，其中的seeLivePlay()就是所谓的**模板方法**，为了不被子类overwrite，它被设置为final的，其定义了一个算法骨架。其中的login()是一个实体方法，里面是通用逻辑，所有的子类都是一样的。四个被abstract修饰的是抽象方法，这些方法是需要子类去根据自己的实际算法实现的，而pushVideoStream()方法有一个默认的空实现，这个一般称为钩子(“**在不侵入原代码的前提下，通过‘预留触发点 + 动态挂载’扩展功能**”)方法，设计用来被其中部分需要的子类overwrite. 例如腾讯直播SDK提供了旁路推流的功能，而金山的没有提供，那么腾讯直播类就可以选择overwrite这个钩子方法，提供自己的实现。

public abstract class LivePlay {

//模板方法

public final void seeLivePlay() {

login();

openRoom();

startAudioAndVideoStream();

pushVideoStream();

stopAudioAndVideoStream();

closeRoom();

}

//实体方法，这个方法实现通用的业务逻辑

private void login() {

System.out.println("用户登录");

}

/\*抽象方法\*/

//打开房间

public abstract void openRoom();

//打开音视频流

public abstract void startAudioAndVideoStream();

//关闭音视频流

public abstract void stopAudioAndVideoStream();

//关闭房间

public abstract void closeRoom();

/\*钩子方法，可以被需要的子类overwrite\*/

//旁路推流，可以通过视频链接在浏览器中查看视频

public void pushVideoStream() {

}

}

**第二步**：定义具体的实体类，根据情况overwrite相应的抽象方法和钩子方法。

//腾讯直播类

public class TencentLivePlay extends LivePlay {

@Override

public void openRoom() {

System.out.println("腾讯打开房间");

}

@Override

public void startAudioAndVideoStream() {

System.out.println("腾讯打开音视频流");

}

@Override

public void stopAudioAndVideoStream() {

System.out.println("腾讯关闭音视频流");

}

@Override

public void closeRoom() {

System.out.println("腾讯关闭房间");

}

//覆写钩子方法，提供旁路推流功能

@Override

public void pushVideoStream() {

super.pushVideoStream();

System.out.println("腾讯进行旁路推流");

}

}

值得注意的是，由于腾讯SDK提供了旁路推流的功能，所以它overwrite了pushVideoStream()这个钩子方法.

//金山直播类

public class [JinShanLivePlay](https://zhida.zhihu.com/search?content_id=102481458&content_type=Article&match_order=1&q=JinShanLivePlay&zhida_source=entity) extends LivePlay {

@Override

public void openRoom() {

System.out.println("金山打开房间");

}

@Override

public void startAudioAndVideoStream() {

System.out.println("金山打开音视频流");

}

@Override

public void stopAudioAndVideoStream() {

System.out.println("金山关闭音视频流");

}

@Override

public void closeRoom() {

System.out.println("金山关闭房间");

}

}

由于金山SDK没有提供了旁路推流的功能，所以它不用覆写pushVideoStream()这个钩子方法,而只需要overwrite抽象方法即可。

第三步: 客户端调用，我们根据后端返回的结果来决定使用哪家的SDK

public static void main(String\[\] args) {

//此处省略若干代码

...

LivePlay tencentLive=new TencentLivePlay();

tencentLive.seeLivePlay();

System.out.println("");

LivePlay jinShanLive=new JinShanLivePlay();

jinShanLive.seeLivePlay();

}

输出为

用户登录

腾讯打开房间

腾讯打开音视频流

腾讯进行旁路推流

腾讯关闭音视频流

腾讯关闭房间

用户登录

金山打开房间

金山打开音视频流

金山关闭音视频流

金山关闭房间

从输出可以清楚的看到，要使用哪家的服务就实例化哪家的对象，然后调用开始观看直播即可。

**优缺点**

**优点**

主要是提高了代码的复用度，而且很好的符合的“开闭原则”。

**缺点**

1.  设计模式的通病：类增多了
2.  调用控制反转：一般情况下，程序的执行流是子类调用父类的方法，模板方法模式使得程序流程变成了父类调用子类方法，这个使得程序比较难以理解和跟踪。