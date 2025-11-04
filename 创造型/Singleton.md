**Singleton**

**类型：**创造型设计模式

**定义：**某个类只有一个实例，且自行实例化并向整个系统提供此实例

**使用场景**

当你希望整个系统运行期间某个类只有一个实例时候。

**单例模式两个核心要点**

- **如何保证单例**

多线程环境下如何保证系统中只有一个实例？如何保证不能通过反射创建新的实例？

- **如何创建单例**

这又分为 **懒汉模式** 与 **饿汉模式**

其实也很好理解，懒汉的意思就是这个类很懒，只要别人不找它要实例，它都懒得创建。饿汉模式正好相反，这个类很着急，非常饥渴的要得到自己的实例，所以一有机会他就创建了自己的实例，不管别人要不要。

1.  **静态常量**

这个简单粗暴，在类加载时候就创建了实例，属于饿汉模式。其是线程安全的，这一点由JVM来保证，但是有一个缺点，可以通过反射创建新的实例。

public class Singleton1 {

private final static Singleton1 INSTANCE = new Singleton1();

private Singleton1() {

}

public static Singleton1 getInstance(){

return INSTANCE;

}

}

1.  **单null检查**

**使用这个写法的程序员应该说水平不是太高，这种写法应该被抛弃**。**其不是线程安全的，也就是说在多线程环境下，系统中有可能存在多个实例**。除此之外，和上面一样通过反射也可以创建新的实例。

public class Singleton2 {

private static Singleton2 instance;

private Singleton2() {

}

public static Singleton2 getInstance() {

if (instance == null) {

instance = new Singleton2();

}

return instance;

}

}

如何解决这个问题，这里不做讲述，你可以自己在网上去寻找答案。

1.  枚举

最牛逼的其实是这哥们儿，如果你的类实现了序列化，那还要防止序列化生成多个实例的问题。而枚举保证了线程安全，保证了反射安全，保证了序列化...

但是，但是，但是实际项目中却很少有人用enum来实现单例...

public enum Singleton6 {

INSTANCE;

}

**总结**

单例模式应该是23种设计模式最简单的一种，日常开发使用空间也极大。