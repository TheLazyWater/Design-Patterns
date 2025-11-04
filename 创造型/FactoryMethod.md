**Factory Method Pattern**

**类型：**创造型模式

有讲过 **_简单工厂模式(Simple Factory Pattern)_** 该模式违背了 开闭原则，因为引入新的产品需要不断的修改 工厂方法，于是工厂方法模式就来了。

**定义**：定义一个用于创建对象的接口，让子类决定实例化哪个类

**使用场景**

工厂方法模式是简单工厂方法模式的升级版本，为了克服简单工厂方法模式的缺点而生，作用和简单工厂方法完全一样。

**UML**

工厂方法模式的核心思想：讨论的仍然是如何构建**同一类**型产品（都实现同一个接口）的问题，只不过是通过为每一种要生产的产品配备一个工厂，就是说每个工厂只生产一种特定的产品。这样做的好处就是当以后需要增加新的产品时，直接新增加一个对应的工厂就可以了，而不是去修改原有的工厂，符合编程原则的**开闭原则**。

工厂方法模式为每一种产品生成一个对应的工厂，从而替换掉简单工厂方法模式中那个静态工厂方法。

**参与者**

|     |     |
| --- | --- |
| Product | 定义一个工厂方法所创建的对象结构 |
| ConcreteProduct | 实现Product接口 |
| Creator | 声明工厂方法，该方法返回一个Product类型的对象。Creator也可以定义一个工厂方法的默认实现，它返回一个默认的ConcreteProduct对象。<br><br>可以调用工厂方法以创建一个Product对象。 |
| ConcreteCreator | 重定义工厂方法以返回一个ConcreteProduct实例。 |

**实例**

现在你的工厂有要求生产其他笔记本电脑，现在安装新的生产线的时候，工人不小心把工厂的线路搞坏了，这太酷…呸…太糟糕了，现在你只能被迫停工几天。

可以看到这就是简单工厂的弊端，**产生新的产品需要去修改工厂方法**，一不小心就出bug了，所以改完了还得调试，测试才能上线，万一没测试好就废了...于是工厂方法模式闪亮登场了。

**代码**

**第一步：定义一个电脑的抽象基类**

里面有一个为电脑安装操作系统的抽象方法，这个就是产品要实现的共同接口。

public abstract class Computer {

public abstract void setOperationSystem();

}

**第二步：实现具体品牌的电脑类**

例如苹果电脑和小米电脑

public class MacComputer extends Computer {

@Override

public void setOperationSystem() {

System.out.println("Mac笔记本安装Mac系统");

}

}

public class MiComputer extends Computer {

@Override

public void setOperationSystem() {

System.out.println("小米笔记本安装Win10系统");

}

}

**第三步：定义工厂**

首先定义一个抽象工厂ComputerFactory接口，里面定义了生产方法，通过这个方法就可以生产出电脑，但是生产不同品牌的电脑的具体方式肯定是不同的，例如生产小米电脑的流水线和生产苹果电脑的流水线肯定是有区别的，所以这个接口方法由具体产品工厂去实现。

public interface ComputerFactory {

Computer makeComputer();

}

下面是实现了生成苹果电脑的MacComputerFactory工厂和生产小米电脑的MiComputerFactory工厂。

public class MacComputerFactory implements ComputerFactory {

@Override

public Computer makeComputer() {

return new MacComputer();

}

}

public class MiComputerFactory implements ComputerFactory {

@Override

public Computer makeComputer() {

return new MiComputer();

}

}

**简单工厂模式**中那个静态工厂方法就被上面的那些工厂类代替了。

**第四步：客户端使用**

使用具体的工厂来生产相应品牌的电脑，例如要生产Mac电脑，就先构建Mac的生产工厂，然后去生产Mac电脑。

public static void main(String\[\] args) {

//生产Mac电脑

ComputerFactory macFactory=new MacComputerFactory();

macFactory.makeComputer().setOperationSystem();

//生产小米电脑

ComputerFactory miFactory=new MiComputerFactory();

miFactory.makeComputer().setOperationSystem();

}

**优缺点**

**优点**：不直接在客户端创建具体产品的实例，降低了耦合性。

**缺点**：每增加一种产品就要相应的增加一个工厂类，类增多了。

**技术要点总结**

1.  构建的都是同一类型的对象，即实现相同的接口
2.  每一类对象对应一个工厂

**相关模式**

1.  Abstract Factory经常用工厂方法来实现。

前者需要创建一系列或相互依赖的产品族，并且**客户端需要使用一个产品族时使用；后者当需要创建单一类型的产品**，且**客户端不知道要创建的具体产品类型，并期待产品类型可能有扩展时使用**。

1.  工厂方法通常在Template Method中被调用
2.  Prototype不需要创建Creator的子类。但是，它们通常需要要求一个针对Product类的Initialize操作。