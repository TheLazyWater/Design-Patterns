**Observer**

**类型**：行为型

**定义**：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都可以得到通知并自动更新。

观察者模式是一个非常常用且影响巨大的设计模式，又称为发布-订阅模式。大致的图可以理解为如下**(也请多多支持原作者**)：

**使用场景**

当你的业务符合**订阅-发布**这种场景时考虑这个模式。

**UML图**

**参与者**

Subject：被观察者，有时也叫Observable，其是一个接口，定义了观察者如何订阅与取消订阅的方法，以及当状态发生变化时的通知方法

ConcreteSunject: 实现Subject，持有Observer的集合。

Observer：观察者，也是一个接口。定义了如何接收事件通知的方法。

ConcreteObserver: 观察着实现类

**实例(原文)：**

王二狗前两天到北京出差见了一个大学同学，两人撸烤串、喝啤酒、忆往昔、吹牛逼...酒过三巡，同学突然说：你知道吗？当年你拼命追求的女神上官无雪离婚了，听说他老公出轨了自己的女下属... 两人提前转移了财产，现在上官那叫一个惨...，你说她当年要是选了你现在多幸福... 二狗陷入了那些当舔狗的峥嵘岁月... 当年二狗和西门淫荡都喜欢上了上官无雪，那时校内网风头正盛，二狗和西门都订阅了上官的的校内网消息，女神的一条想法，就是二狗和西门行动的命令...

这个案例对应到程序上就是**二狗和西门都在观察上官的状态，上官的每一条状态改变都通过校内网通知了二狗和西门，于是他们根据这个事件做出行动**。

**代码实现**

被观察者，或者角订阅主题

首先是上官无雪这个绿茶婊的类

先定义一个接口，很简单，包含一个用来订阅的方法 add 和取消订阅的方法 remove。还有一个当状态发生改变时，用来通知其观察者的方法 notifyState

public interface GreenTeaBitchSubject {

void add(TianDogObserver observer);

void remove(TianDogObserver observer);

void notifyState(String state);

}

接下来是其实现类

public class ShangGuanSubject implements GreenTeaBitchSubject{

private List&lt;TianDogObserver&gt; tianDogs = new ArrayList<>();

@Override

public void add(TianDogObserver observer) {

tianDogs.add(observer);

}

@Override

public void remove(TianDogObserver observer) {

tianDogs.remove(observer);

}

@Override

public void notifyState(String state) {

for (TianDogObserver tianDog : tianDogs) {

tianDog.update(state);

}

}

}

也非常简单，其持有一个对其感兴趣的观察者集合，当状态发生改变时就循环通知每个观察者。

**观察者，也可以叫做订阅者**。

先定义一个观察者接口，里面只有一个方法，观察者就是在这个方法里面接收被观察者发送的状态改变通知。

public interface TianDogObserver {

void update(String message);

}

这里我们实现一个舔狗王二狗类

public class Dog2WangObserver implements TianDogObserver{

@Override

public void update(String message) {

if("肚子好饿".equals(message)){

System.out.println("二狗：半夜起来，翻墙出去步行2公里买女神最爱吃的小笼包... 上官：二狗，你真是个好人");

}else if("心情不好".equals(message)){

System.out.println("二狗：官哭着述说渣男怎么欺负的她...整整3个小时，全程安慰... 上官：二狗，你真是个好人");

}else {

System.out.println("随叫随到...");

}

}

}

可以看到，在update方法中，接收到女神的通知后，根据不同的状态做出了相应的动作。

**客户端的使用**

客户端调用时，先给被观察者通过

add

添加观察者，也就是让二狗和西门订阅上官的状态，以便于上官状态改变时可以得到通知。最后使用

notifyState

来通知观察者状态改变了。如果某个观察者不再对被观察者感兴趣，直接使用

remove

方法取消订阅即可。最后王二狗被绿茶婊上官伤透了心，和牛翠华走到了一起，从此二狗就不再关心上官的状态了，直接使用

remove

方法取消订阅。

public class ObserverClient {

public void sendThink(){

GreenTeaBitchSubject subject = new ShangGuanSubject();

Dog2WangObserver dog2WangObserver = new Dog2WangObserver();

subject.add(dog2WangObserver);

subject.add(new YinDangObserver());

System.out.println("------午夜12点，肚子好饿，找个人来买东西吃------");

subject.notifyState("肚子好饿");

System.out.println("------又被渣男甩了，有点小难过，找个人来安慰一下------");

subject.notifyState("心情不好");

System.out.println("------二狗终于看透了上官这个绿茶婊，和牛翠花走到了一起------");

subject.remove(dog2WangObserver);

System.out.println("------上官又被渣男甩了，想找个人来安慰一下，却不见了那个好人的身影------");

subject.notifyState("心情不好");

}

}

输出

\------午夜12点，肚子好饿，找个人来买东西吃------

二狗：半夜起来，翻墙出去步行2公里买女神最爱吃的小笼包... 上官：二狗，你真是个好人

西门：是吗？那我下面给你吃啊？上官：讨厌拉...

\------又被渣男甩了，有点小难过，找个人来安慰一下------

二狗：官哭着述说渣男怎么欺负的她...整整3个小时，全程安慰... 上官：二狗，你真是个好人

西门：南门那边新开了一家酒吧，晚上带你去放松一下吧... 上官：好嗨吆，感觉人生达到了高峰..

\------二狗终于看透了上官这个绿茶婊，和牛翠花走到了一起------

\------上官又被渣男甩了，想找个人来安慰一下，却不见了那个好人的身影------

西门：南门那边新开了一家酒吧，晚上带你去放松一下吧... 上官：好嗨吆，感觉人生达到了高峰..

**技术要点总结**

1.  被观察者，Subject 或者叫Observable 需持有observer的集合
2.  观察者在自己接收通知的方法中获取被观察者的状态以及通知