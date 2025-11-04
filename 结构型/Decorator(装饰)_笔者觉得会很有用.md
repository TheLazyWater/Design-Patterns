**Decorator**

**类型**：结构型

**定义：**装饰模式是在**不必改变原类**和**使用继承的情况下**，**动态地扩展一个对象的功能**。它是**通过创建一个包装对象**，也就是装饰来包裹真实的对象。

**使用场景**

1.  需要在运行时动态的给一个对象增加额外的职责时候
2.  需要给一个现有的类增加职责，但是又不想通过继承的方式来实现的时候（应该优先使用组合而非继承），或者通过继承的方式不现实的时候（可能由于排列组合产生类爆炸的问题）。

**具体业务场景**

假设我们有一个原味咖啡的类 OriginalCoffee， 目前的需求就是要**动态**的给这个类的**一些实例**增加一些**额外功能**，此处就是动态的对某些咖啡制作过程增加新的流程，例如加奶，加糖，而有的咖啡却保持原味不变。

这种需求要是通过继承的方式就不太好实现，因为咖啡制作过程是动态变化的。例如有的需要原味咖啡，有的需要加奶咖啡，有的需要加糖咖啡，而有的需要先加奶再加糖咖啡，而有的需要先加糖再加奶的咖啡，。。。这是一个排列组合问题，如果使用类继承的方式实现，**必须预先**将所有可能组合都想清楚，然后生成相应的子类，随着咖啡口味的增多，以及添加顺序的改变，几乎是不可扩展和维护的。

经过需求分析，二狗锁定了装饰者模式来实现此需求。原味咖啡是本质，而加奶，加糖都是在装饰这个本质的东西，再怎么加东西咖啡还是咖啡。

UML图片

首先我们有一个ICoffee接口，里面有一个制作咖啡的接口方法makeCoffee()。要进行装饰的类 OriginalCoffee 和装饰者基类CoffeeDecorator（一般为抽象类）实现了此接口。CoffeeDecorator类里面持有一个ICoffee引用，我们第一步会把要装饰那个原始对象赋值给这个引用，那样在装饰者类中才可以调用到那个被装饰的对象的方法。**MilkDecorator 和SugarDecorator 都继承至CoffeeDecorator， 都是具体的装饰者类**。

**参与者**

|     |     |
| --- | --- |
| Component | 定义一个对象接口，可以给这些对象动态地添加职责。 |
| ConcreteComponent | 定义一个对象，可以给这个对象添加一些职责 |
| Decorator | 维持一个指向Component对象的指针，并定义一个与Component接口一致的接口。 |
| ConcreteDecorator | 向组件添加职责。 |

**具体实现**

**第一步：先声明一个原始对象的接口**

public interface ICoffee {

void makeCoffee();

}

**第二步：构建我们的原始对象，此处为原味咖啡对象，它实现了ICoffee接口。**

public class OriginalCoffee implements ICoffee {

@Override

public void makeCoffee() {

System.out.print("原味咖啡 ");

}

}

**第三步：构建装饰者抽象基类，它要实现与原始对象相同的接口ICoffee，其内部持有一个ICoffee类型的引用，用来接收被装饰的对象**

public abstract class CoffeeDecorator implements ICoffee {

private ICoffee coffee;

public CoffeeDecorator(ICoffee coffee){

this.coffee=coffee;

}

@Override

public void makeCoffee() {

coffee.makeCoffee();

}

}

**第四步：构建各种装饰者类，他们都继承至装饰者基类 CoffeeDecorator。此处生成了两个，一个是加奶的装饰者,另一个是加糖的装饰者。**

public class MilkDecorator extends CoffeeDecorator {

public MilkDecorator(ICoffee coffee) {

super(coffee);

}

@Override

public void makeCoffee() {

super.makeCoffee();

addMilk();

}

private void addMilk(){

System.out.print("加奶 ");

}

}

public class SugarDecorator extends CoffeeDecorator {

public SugarDecorator(ICoffee coffee) {

super(coffee);

}

@Override

public void makeCoffee() {

super.makeCoffee();

addSugar();

}

private void addSugar(){

System.out.print("加糖");

}

}

**第五步：客户端使用**

public static void main(String\[\] args) {

//原味咖啡

ICoffee coffee=new OriginalCoffee();

coffee.makeCoffee();

System.out.println("");

//加奶的咖啡

coffee=new MilkDecorator(coffee);

coffee.makeCoffee();

System.out.println("");

//先加奶后加糖的咖啡

coffee=new SugarDecorator(coffee);

coffee.makeCoffee();

}

**输出：**

原味咖啡

原味咖啡 加奶

原味咖啡 加奶 加糖

**优缺点**

**优点：**

可以提供比继承更加灵活的方式去扩展对象的功能，通过排列组合，可以对某个类的一些对象做动态的功能扩展，而不需要装饰的对象却可以保持原样。

**缺点：**

仍然是设计模式的通用缺点：类的个数会增加，会产生很多装饰者类，相应的就增加了复杂度。

**装饰者模式与代理模式的区别**

一般认为代理模式侧重于使用代理类增强被代理对象的访问，而装饰者模式侧重于使用装饰者类来对被装饰对象的功能进行增强。 除了上面的区别，个人实践中还发现，装饰者模式主要是提供一组装饰者类，然后形成一个**装饰者栈**，来动态的对某一个对象不断加强，而代理一般不会使用多级代理，详情请见

**总结**

装饰模式的核心是“动态包装，功能叠加”，通过“抽象组件 + 装饰器嵌套”实现对象功能的灵活扩展，避免了继承带来的类爆炸和静态限制。它尤其适合处理“可选功能多样且需要动态组合”的场景，是“开闭原则”的典型应用（新增功能只需新增装饰器，无需修改原有代码）。