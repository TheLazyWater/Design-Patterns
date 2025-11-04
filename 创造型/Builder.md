**Build**

**类型：**创造性模式

没写过5万行代码谈设计模式就是扯淡——牛逼哄哄的能量瓶小子

**定义：**将一个复杂对象的构建与其表示分离，使得同样的构建过程可以创建不同的表示。

**使用场景：**

**当一个类的构造函数参数个数超过4个，而且这些参数有些是可选的参数，考虑使用构造者模式。**

**解决问题：**

当**一个类的构造函数参数超过4个，而且这些参数有些是可选的时**，我们通常有两种办法来构建它的对象。 例如我们现在有如下一个类计算机类Computer，其中cpu与ram是必填参数，而其他3个是可选参数，那么我们如何构造这个类的实例呢：

public class Computer {

private String cpu;//必须

private String ram;//必须

private int usbCount;//可选

private String keyboard;//可选

private String display;//可选

}

**第一**：折叠构造函数模式(telescoping constructor pattern)，这个是很经典的解决方案。

public class Computer {

...

public Computer(String cpu, String ram) {

this(cpu, ram, 0);

}

public Computer(String cpu, String ram, int usbCount) {

this(cpu, ram, usbCount, "罗技键盘");

}

public Computer(String cpu, String ram, int usbCount, String keyboard) {

this(cpu, ram, usbCount, keyboard, "三星显示器");

}

public Computer(String cpu, String ram, int usbCount, String keyboard, String display) {

this.cpu = cpu;

this.ram = ram;

this.usbCount = usbCount;

this.keyboard = keyboard;

this.display = display;

}

}

第一种主要是使用及阅读不方便。你可以想象一下，当你要调用一个类的构造函数时，你首先要决定使用哪一个，然后里面又是一堆参数，如果这些参数的类型很多又都一样，你还要搞清楚这些参数的含义，很容易就传混了…那酸爽谁用谁知道。

为了解决这两个痛点，builder模式就横空出世了！

**如何实现(Java)：**

1.  在Computer 中创建一个静态内部类 Builder，然后将Computer 中的参数都复制到Builder类中。
2.  在Computer中创建一个private的构造函数，参数为Builder类型
3.  在Builder中创建一个public的构造函数，参数为Computer中必填的那些参数，cpu 和ram。
4.  在Builder中创建设置函数，对Computer中那些可选参数进行赋值，返回值为Builder类型的实例
5.  在Builder中创建一个build()方法，在其中构建Computer的实例并返回

代码如下：

public class Computer {

private final String cpu;//必须

private final String ram;//必须

private final int usbCount;//可选

private final String keyboard;//可选

private final String display;//可选

private Computer(Builder builder){

this.cpu=builder.cpu;

this.ram=builder.ram;

this.usbCount=builder.usbCount;

this.keyboard=builder.keyboard;

this.display=builder.display;

}

public static class Builder{

private String cpu;//必须

private String ram;//必须

private int usbCount;//可选

private String keyboard;//可选

private String display;//可选

public Builder(String cup,String ram){

this.cpu=cup;

this.ram=ram;

}

public Builder setUsbCount(int usbCount) {

this.usbCount = usbCount;

return this;

}

public Builder setKeyboard(String keyboard) {

this.keyboard = keyboard;

return this;

}

public Builder setDisplay(String display) {

this.display = display;

return this;

}

public Computer build(){

return new Computer(this);

}

}

//省略getter方法

}

**如何使用**

在客户端**使用链式调用**，一步一步的把对象构建出来。

Computer computer=new Computer.Builder("因特尔","三星")

.setDisplay("三星24寸")

.setKeyboard("罗技")

.setUsbCount(2)

.build();

**注意**：**C#也可以使用链式方式，实例化**。

**案例**

构建者模式是一个非常实用而常见的创建类型的模式（creational design pattern)，例如图片处理框架Glide，网络请求框架[Retrofit](https://zhida.zhihu.com/search?content_id=101039286&content_type=Article&match_order=1&q=Retrofit&zhida_source=entity)等都使用了此模式。

**扩展**

其实上面的内容是Builder在Java中一种简化的使用方式，**经典的Builder 模式与其有一定的不同**，如果没有兴趣的同学就可以不用往下读了。

**传统Builder模式**

**UML图**

**参与者**

<div class="joplin-table-wrapper"><table><tbody><tr><td><p>Builder</p></td><td><p>为创建一个Product对象的各个部件制定抽象接口。</p></td></tr><tr><td><p>ConcreteBuilder(具体创建者)</p></td><td><ul><li>实现Builder的接口以构造和装配该产品的各个部件。</li><li>定义并跟踪它所创建的标识</li><li>提供一个检索产品的接口</li></ul></td></tr><tr><td><p>Director(主管,Builder的使用方法)</p></td><td><p>构造一个使用Builder接口的对象</p></td></tr><tr><td><p>Product</p></td><td><ul><li>表示被构造的复杂对象。ConcreteBuilder创建该产品的内部表示并定义它的装配过程。</li><li>包含定义组成部件的类，包括将这些部件装配成最终产品的接口。</li></ul><p><strong>人话：最终要生成的对象</strong></p></td></tr></tbody></table></div>

还是使用最开始的例子，这次使用传统方法实现一遍

第一步：目标Computer类

public class Computer {

private String cpu;//必须

private String ram;//必须

private int usbCount;//可选

private String keyboard;//可选

private String display;//可选

public Computer(String cpu, String ram) {

this.cpu = cpu;

this.ram = ram;

}

public void setUsbCount(int usbCount) {

this.usbCount = usbCount;

}

public void setKeyboard(String keyboard) {

this.keyboard = keyboard;

}

public void setDisplay(String display) {

this.display = display;

}

@Override

public String toString() {

return "Computer{" +

"cpu='" + cpu + '\\'' +

", ram='" + ram + '\\'' +

", usbCount=" + usbCount +

", keyboard='" + keyboard + '\\'' +

", display='" + display + '\\'' +

'}';

}

}

第二步：抽象类构建者

public abstract class ComputerBuilder {

public abstract void setUsbCount();

public abstract void setKeyboard();

public abstract void setDisplay();

public abstract Computer getComputer();

}

第三步：实体构建者类，我们可以根据要构建的产品种类产生多了实体构建者类，这里我们需要构建两种品牌的电脑，苹果电脑和联想电脑，所以我们生成了两个实体构建者类。

苹果电脑构建者类

public class MacComputerBuilder extends ComputerBuilder {

private Computer computer;

public MacComputerBuilder(String cpu, String ram) {

computer = new Computer(cpu, ram);

}

@Override

public void setUsbCount() {

computer.setUsbCount(2);

}

@Override

public void setKeyboard() {

computer.setKeyboard("苹果键盘");

}

@Override

public void setDisplay() {

computer.setDisplay("苹果显示器");

}

@Override

public Computer getComputer() {

return computer;

}

}

联想电脑构建者类

public class LenovoComputerBuilder extends ComputerBuilder {

private Computer computer;

public LenovoComputerBuilder(String cpu, String ram) {

computer=new Computer(cpu,ram);

}

@Override

public void setUsbCount() {

computer.setUsbCount(4);

}

@Override

public void setKeyboard() {

computer.setKeyboard("联想键盘");

}

@Override

public void setDisplay() {

computer.setDisplay("联想显示器");

}

@Override

public Computer getComputer() {

return computer;

}

}

第四步：指导者类(Director)

public class ComputerDirector {

public void makeComputer(ComputerBuilder builder){

builder.setUsbCount();

builder.setDisplay();

builder.setKeyboard();

}

}

**使用：**

首先生成一个director (1)，然后生成一个目标builder (2)，接着使用director组装builder (3),组装完毕后使用builder创建产品实例 (4)。

public static void main(String\[\] args) {

ComputerDirector director=new ComputerDirector();//1

ComputerBuilder builder=new MacComputerBuilder("I5处理器","三星125");//2

director.makeComputer(builder);//3

Computer macComputer=builder.getComputer();//4

System.out.println("mac computer:"+macComputer.toString());

ComputerBuilder lenovoBuilder=new LenovoComputerBuilder("I7处理器","海力士222");

director.makeComputer(lenovoBuilder);

Computer lenovoComputer=lenovoBuilder.getComputer();

System.out.println("lenovo computer:"+lenovoComputer.toString());

}

可以看到，文章最开始的使用方式是传统builder模式的变种， 首先其省略了director 这个角色，将构建算法交给了client端，其次将builder 写到了要构建的产品类里面，最后采用了链式调用

**总结**

Builder 模式的核心作用可以概括为：**当对象需要多个参数，尤其是存在可选参数构建时，通过 “分步设置 + 最终构建” 的方式，替代复杂的重载构造函数或不安全的 setter 方法，确保对象构建的灵活性、可读性和状态一致性**。

Builder着重于一步步构建复杂对象。

**实现Builder一些有用的技术(原文)：**

Composite(组合)通常是Builder生成的