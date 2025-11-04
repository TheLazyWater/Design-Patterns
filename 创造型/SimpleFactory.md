**SimpleFactory**

**类型：**创造性模式

工厂模式一般有3种，今天研究的**简单工厂模式不在GOF的经典23种设计模式之中**，但它却足够简单，非常适合日常开发中解决相应场景的问题，人人都学的会。

创建型设计模式讨论的是如何创建对象的问题。就是以各种高逼格的方式最终将一个类的对象**new**出来，一点也不神秘，简单工厂模式就属于此类型。

记住这句话，只是为了new出来，也不是什么高大上的东西，不去扯工厂模式要比直接new好——LazyWater

**定义:**简单工厂模式，又叫做静态工厂方法模式，是由一个工厂对象决定创建出哪一种产品类的实例。

**使用场景**

最重要的知识点，这是最重要的知识点，这是最重要的知识点！不能利用技术来解决实际问题，技术将一文不值——shusheng007

首先需要new一个类的对象的时，各种状况就出现了：

1.  你不想直接new这个类的对象，怕以后这个类改变的时候你需要回来改代码，而此时依赖这个类的地方已经到处都是了。
2.  这个类的对象构建过程非常复杂，你不愿意将这么复杂的构建过程一遍又一遍的写在需要用到此对象的地方。
3.  这个类的对象在构建过程中依赖了很多其他的类，而你无法在调用的地方提供。

**UML类图**

参与者

|     |     |
| --- | --- |
| 对象工厂 |     |
| 要生产的对象接口 |     |
| 要生产的对象 |     |

MacComputer 与MiComputer继承了Computer类，SimpleComputerFactory类可以根据不同的条件来生成相应品牌的电脑。

简单工厂模式的核心就是**通过一个工厂方法根据不同的条件生产同一类型的产品**。例如此例中我们要生产的小米笔记本和苹果笔记本他们是同一类型的产品，会实现**同一个**接口。

**实例**

**第一步：定义对象抽象基类**

定义一个电脑的抽象基类，包含一个为电脑安装操作系统的抽象方法，这个就是产品要实现的共同接口。

public abstract class Computer {

public abstract void setOperationSystem();

}

**第二步：定义具体的对象类**

定义具体品牌的电脑类，苹果电脑和小米电脑

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

**第三步：定义简单工厂方法类**

使用一个静态工厂方法来根据不同的条件来产生不同的产品实例

public class SimpleComputerFactory {

public static Computer makeComputer(String brand) {

Computer computer=null;

switch (brand) {

case "mac":

computer=new MacComputer();

break;

case "mi":

computer=new MiComputer();

break;

default:

break;

}

return computer;

}

}

第四步：客户端的使用

public static void main(String\[\] args) {

Computer computer= SimpleComputerFactory.makeComputer("mi");

computer.setOperationSystem();

}

不使用设计模式

如果我们不使用设计模式能不能实现这个业务呢？答案当然是肯定的了。那就直接**new**对象咯，如下代码所示。好像不使用设计模式代码更简洁啊？这是因为此处的代码仅仅是为了演示而设计的异常简单的没有实用价值的代码，实际项目中对象之间的关系及构建过程比这个复杂的多，而且要不断的扩展和维护，这才是设计模式被引入软件系统设计的目的。

public static void main(String\[\] args) {

Computer miComputer=new MiComputer();

miComputer.setOperationSystem();

}

**优点**：不直接在客户端创建具体产品的实例，降低了耦合性。

**缺点**：违反了开闭原则，（对扩展开放，对修改关闭），不容易形成高内聚松耦合结构。 每当我们增加一种产品的时候就要去修改工厂方法，这样会破坏其内聚性，给维护带来额外开支。为了克服简单工厂方法模式的缺点，**_工厂方法模式_**就被提了出来…

**总结**

没什么好总结的，多用设计模式思考，你就明白什么时候该用什么。一切都是经验之谈，**_设计模式是一种思想，一种非强制性的规则_**。有时候想不明白就直接去做，想到了再回来处理优化——船到桥头自然直。