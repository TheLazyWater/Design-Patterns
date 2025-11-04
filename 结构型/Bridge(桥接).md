**Bridge——对象结构型模式**

**类型**：结构型

**定义**：桥接模式是**_将抽象部分与它的实现部分分离_**，使它们都可以独立地变化。它是一种对象结构型模式，又称为柄体(Handle and Body)模式或接口(Interface)模式。

**使用场景**

**桥**我们大家都熟悉，顾名思义就是用来将河的两岸联系起来的。而此处的桥是用来将两个独立的结构联系起来，而**_这两个被联系起来的结构可以独立的变化_**，所有其他的理解只要建立在这个层面上就会比较容易。

下面是一些官方的说明，比较晦涩，必须等你有一定的经验后才能理解： 1. 如果一个系统需要在抽象化和具体化之间增加更多的灵活性，避免在两个层次之间建立静态的继承关系，通过桥接模式可以使它们在抽象层建立一个关联关系。

1.  “抽象部分”和“实现部分”可以以继承的方式独立扩展而互不影响，在程序运行时可以动态将一个抽象化子类的对象和一个实现化子类的对象进行组合，即系统需要对抽象化角色和实现化角色进行动态耦合。
2.  一个类存在两个（或多个）独立变化的维度，且这两个（或多个）维度都需要独立进行扩展。
3.  对于那些不希望使用继承或因为多层继承导致系统类的个数急剧增加的系统，桥接模式尤为适用。

**如何实现——以下是shusheng007前辈的原文**

在讲策略模式的时候，王二狗和牛翠花不是要到天津之眼去约会，两人到那后先去星巴克喝咖啡了，**星巴克提供了多种选择，从容量上说有大杯，中杯，小杯，从口味上说有原味，加糖，**这可难为了有选择恐惧症的牛翠花，半天点不出来，后面的人都开始骂娘了，王二狗也只能陪着笑脸道歉。 其实被难为的除了牛翠花，还有给星巴克做订单系统的外包公司的程序员林蛋大。一开始提需求的时候星巴克说我们只有正常杯（中杯），原味和加糖这几种选择，人家林蛋大也是有两年工作经验的码农，这需求不在话下，蛋大还想到了要面对抽象编程。

首先定义一个点咖啡接口，里面有一个下单方法，至于点哪种口味的咖啡，就由其子类去决定，完美，蛋大好牛逼啊！

public interface ICoffee {

void orderCoffee(int count);

}

原味咖啡类

public class CoffeeOriginal implements ICoffee {

@Override

public void orderCoffee(int count) {

System.out.println(String.format("原味咖啡%d杯",count));

}

}

加糖咖啡类

public class CoffeeWithSugar implements ICoffee {

@Override

public void orderCoffee(int count) {

System.out.println(String.format("加糖咖啡%d杯",count));

}

}

搞定收工，王者荣耀搞起！项目经理突然过来了：蛋大啊，客户那边说了，他们准备加两个容量规格的咖啡，大杯和小杯，你改一下。林蛋大：what？尼玛为什么不早说，老子刚写完。胸中虽有万千牢骚，还不得平复一下心情去改代码，何必呢？蛋大心中暗喜，幸好老子是面向抽象编程的，对应加几个实现类不就得了？

//中杯加糖

public class CoffeeWithSugar implements ICoffee {

@Override

public void orderCoffee(int count) {

System.out.println(String.format("中杯加糖咖啡%d杯",count));

}

}

//大杯加糖

public class LargeCoffeeWithSugar implements ICoffee {

@Override

public void orderCoffee(int count) {

System.out.println(String.format("大杯加糖咖啡%d杯",count));

}

}

加着加着蛋大慌了，共需要3x2=6个类啊，大杯原味和加糖，中杯原味和加糖，小杯原味和加糖。过段时间万一那二笔客户又要出加奶，加蜂蜜等等口味，说不定还有迷你杯，女神杯等等规格的咖啡，那我这边的类不就爆炸了吗？看来的去找个设计模式了。。。

此场景桥接模式正合适，**这里有两个变化维度，咖啡的容量和口味**，**而且都需要独立变化**。如果使用继承的方式，随着变化类就会急剧的增加。你可以将容量理解为抽象部分，而口味理解为实现部分，这两个部分需要桥接。

**使用桥接模式**

**UML图**

**参与者**

<div class="joplin-table-wrapper"><table><tbody><tr><td><p>Abstraction</p></td><td><p>定义抽象类的接口</p><p>维护一个指向Implementor类型对象的指针。</p></td></tr><tr><td><p>RefinedAbstraction(修正抽象化角色)</p></td><td><p>扩充由Abstraction定义的接口，改变和修正父类对抽象化的定义。您可以理解为对功能的扩展，比如添加的特殊用途，一个更高级的系统。Abstraction存放的是通用用途。</p></td></tr><tr><td><p>Implementor(实现化)</p></td><td><ul><li>定义实现类的接口，该接口不一定要与Abstraction的接口完全一致，事实上这两个接口可以完全不同。一般来讲，Implementor接口仅提供基本操作，而Abstraction则定义了基于这些基本操作的较高层次的操作。</li><li>这个角色给出实现化角色的接口，但不给出具体的实现。必须指出的是，这个接口不一定和抽象化角色的接口定义相同，实际上，这两个接口可以非常不一样。实现化角色应当只给出底层操作，而抽象化角色应当只给出基于底层操作的更高一层的操作。</li></ul></td></tr><tr><td><p>ConcreteImplementor(具体实现化)</p></td><td><ul><li>实现Implementor接口并定义它的具体实现。</li><li>这个角色给出实现化角色接口的具体实现</li></ul></td></tr></tbody></table></div>

抽象化角色就像是一个水杯的手柄，而实现化角色和具体实现化角色就像是水杯的杯身。手柄控制杯身，这就是此模式别名“柄体”的来源。

分析了当前业务场景，认为可以将咖啡的容量作为**抽象化Abstraction**，而咖啡口味为**实现化Implementor**

**第一步：创建抽象化部分**

//抽象化Abstraction

public abstract class Coffee {

protected ICoffeeAdditives additives; //**ICoffeeAdditives是指咖啡口味的接口**

public Coffee(ICoffeeAdditives additives){

this.additives=additives;

}

public abstract void orderCoffee(int count);

}

我们可以看到，Coffee持有了ICoffeeAdditives 引用，ICoffeeAdditives 的实例是通过构造函数注入的，这个过程就是我们所说的**_桥接_**过程。我们通过这个引用就可以调用ICoffeeAdditives的方法，进而将Coffee的行为与ICoffeeAdditives的行为通过orderCoffee()方法而组合起来。

下面是一个对抽象化修正的一个类,里面增加了一个品控的方法，**只是进一步的扩展逻辑。**

//RefinedAbstraction

public abstract class RefinedCoffee extends Coffee {

public RefinedCoffee(ICoffeeAdditives additives) {

super(additives);

}

public void checkQuality(){

Random ran=new Random();

System.out.println(String.format("%s 添加%s",additives.getClass().getSimpleName(),ran.nextBoolean()?"太多":"正常"));

}

}

**第二步：创建实现化部分**

public interface ICoffeeAdditives {

void addSomething();

}

//加奶

public class Milk implements ICoffeeAdditives {

@Override

public void addSomething() {

System.out.println("加奶");

}

}

//加糖

public class Sugar implements ICoffeeAdditives {

@Override

public void addSomething() {

System.out.println("加糖");

}

}

**第三部：客户端调用**

public static void main(String\[\] args) {

//点两杯加奶的大杯咖啡

RefinedCoffee largeWithMilk=new LargeCoffee(new Milk());

largeWithMilk.orderCoffee(2);

largeWithMilk.checkQuality();

}

输出结果：

加奶

大杯咖啡2杯

Milk 添加太多

通过使用桥接模式，就使得咖啡的容量和口味这两个维度可以独立变化，互不干扰。林蛋大终于完成了代码的重构，回头一看王者荣耀由于恶意挂机被扣除15个信用分，暂时不能进行排位赛，无奈只能再去巩固一下桥接模式了。

**优缺点**

**优点**

1.  分离抽象接口及其实现部分。桥接模式使用“对象间的关联关系”解耦了抽象和实现之间固有的绑定关系，使得抽象和实现可以沿着各自的维度来变化。所谓抽象和实现沿着各自维度的变化，也就是说抽象和实现不再在同一个继承层次结构中，而是“子类化”它们，使它们各自都具有自己的子类，以便任何组合子类，从而获得多维度组合对象。
2.  在很多情况下，桥接模式可以取代多层继承方案，多层继承方案违背了“单一职责原则”，复用性较差，且类的个数非常多，桥接模式是比多层继承方案更好的解决方法，它极大减少了子类的个数。
3.  桥接模式提高了系统的可扩展性，在两个变化维度中任意扩展一个维度，都不需要修改原有系统，符合“开闭原则”。

**缺点**

1.  桥接模式的使用会增加系统的理解与设计难度，由于关联关系建立在抽象层，要求开发者一开始就针对抽象层进行设计与编程。
2.  桥接模式要求正确识别出系统中两个独立变化的维度，因此其使用范围具有一定的局限性，如何正确识别两个独立维度也需要一定的经验积累。

**总结**

暂无