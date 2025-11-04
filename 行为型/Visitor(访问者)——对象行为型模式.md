**Visitor**

**类型**：行为型

**定义**：封装一些作用于某种数据结构中的各元素的操作，它可以在不改变这个数据结构的前提下定义作用于其内部各个元素的新操作

访问者模式可以说是GOF23中设计模式中最复杂的一个，但日常开发中使用频率却不高，所以说上帝喜欢简洁！增删改查虽然简单，却是大部分程序员日常主要工作，是可以混饭吃的家伙式。你技术再牛逼，企业用不到，那对于企业来说也没屌用，所以说合适的才是最好的。但不常用不等于没有用，这一点的认识到。

访问者模式试图解决如下问题： 一个类**农场**里面包含各种元素，例如有大雁，狗子，鸭子。而每个元素的操作却经常变换，一会让大雁排成一字，一会让大雁排成人字。当大雁排成一字的时候狗子要排成S形状，鸭子要排成B形状，当大雁排成人字时候狗子要叫两声，鸭子要跳起来...。但对**农场**这类有要求，**第一：可以迭代这些元素，第二：里面的元素不能频繁变动**，你不能一会把鸭子杀了吃了，一会又买回一匹马...，如果是这样的话就不适合使用Visitor模式

**难度**：**沙皇炸弹**

**使用场景**

当你有个类，里面的包含各种类型的元素，这个类结构比较稳定，不会经常增删不同类型的元素。而需要经常给这些元素添加新的操作的时候，考虑使用此设计模式。(**元素不改变，只需要添加新的行为**)

**UML图**

参与者

- ObjectStructure：这个角色就是我们的对象结构，对应上面的大忽悠科技有限公司：BigHuYouCompany。此对象结构包含各种元素，而且要求元素稳定且可以迭代访问这些元素。
- Visitor：大名鼎鼎的访问者，它是一个接口。里面定义了与元素对应的visite(Element)方法，一般是有几个元素就相应的有几个visite方法。
- ConcreteVisitor：visitor的实现类
- Element：是一个接口，代表在ObjectStructure里面的元素。里面定义了一个accept(Visiotr)的方法，通过此方法元素可以将自己交给Visitor访问。
- ConcreteElement: element 的实现类。

**实例**

王二狗刚参加工作那会由于社会经验不足误入了一个大忽悠公司，公司老板不舍得花钱就给公司招了3个人，一个Hr，一个程序员，一个测试，但关键是老板总想追风口，啥都想做，一会社交，一会短视频。二狗多次提出说人太少，申请加几个人，至少加个保洁阿姨啊，每天都自己打扫卫生，累屁了。每到此时老板就画大饼：你现在刚毕业正是要奋斗的时候，此时不奋斗什么时候奋斗？过两年公司上市了，你作为元老就财富自由拉...balabala

这个场景就很适合使用访问者模式：

大忽悠公司结构很稳定，老板舍不得花钱招人，总共就那么3个人，还是3种角色，即只有3个元素。 大忽悠公司老板想法多，这就要求这3个人承担各种新技能，即不断的给元素增加新的算法。

第一步：构建Element

毕竟改变的是元素的算法，所以这里我们先构建元素。

社畜类只有一个accept方法，它需要一个访问者接口类型的参数

public interface CorporateSlave {

void accept(CorporateSlaveVisitor visitor);

}

构建3个社畜的实现类

- 程序员

public class Programmer implements CorporateSlave {

private String name;

public Programmer(String name) {

this.name = name;

}

public String getName() {

return name;

}

@Override

public void accept(CorporateSlaveVisitor visitor) {

visitor.visit(this);

}

}

- 测试

public class Tester implements CorporateSlave {

private String name;

public Tester(String name) {

this.name = name;

}

public String getName() {

return name;

}

@Override

public void accept(CorporateSlaveVisitor visitor) {

visitor.visit(this);

}

}

人力

同上，省略

...

**注意在element类里面将自己传递给visitor的visit()方法**

@Override

public void accept(CorporateSlaveVisitor visitor) {

visitor.visit(this);

}

第二步：构建ObjectStructure

BigHuYouCompany 类里面需要包含相对稳定的元素（大忽悠老板就招这3个人，再也不肯招人），而且要求可以对这些元素迭代访问。此处我们以集合存储3位员工。

/\*\*

\* Copyright (C) 2021 ShuSheng007

\* 完全享有此软件的著作权

\*

\* @author ShuSheng007

\* @time 2021/6/7 23:01

\* @description

\*/

public class BigHuYouCompany {

private List&lt;CorporateSlave&gt; employee= new ArrayList<>();

public BigHuYouCompany() {

employee.add(new Programmer("王二狗"));

employee.add(new HumanSource("上官无需"));

employee.add(new Tester("牛翠花"));

}

public void startProject(CorporateSlaveVisitor visitor){

for (CorporateSlave slave : employee) {

slave.accept(visitor);

}

}

}

**第三步：构建Visitor**

Visitor 接口里面一般会存在与各元素对应的visit方法，例如此例我们有3个角色，所以这里就有3个方法。

/\*\*

\* Copyright (C) 2021 ShuSheng007

\* 完全享有此软件的著作权

\*

\* @author ShuSheng007

\* @time 2021/6/7 21:54

\* @description

\*/

public interface CorporateSlaveVisitor {

void visit(Programmer programmer);

void visit(HumanSource humanSource);

void visit(Tester tester);

}

Visitor实现类

因为老板觉得社交是人类永恒的需求，所以开始想做社交App，他觉得他能成为微信第二。

这就相当于要为每一个元素定义一套新的算法，让程序员仿照微信开发设计app，让测试完成即时通信的测试，让人力发软文。

public class SocialApp implements CorporateSlaveVisitor {

@Override

public void visit(Programmer programmer) {

System.out.println(String.format("%s: 给你一个月，先仿照微信搞个类似的APP出来,要能语音能发红包,将来公司上市了少不了你的，好好干...",programmer.getName()));

}

@Override

public void visit(HumanSource humanSource) {

System.out.println(String.format("%s: 咱现在缺人，你暂时就充当了陪聊吧，在程序员开发APP期间，你去发发软文，积攒点粉丝...",humanSource.getName()));

}

@Override

public void visit(Tester tester) {

System.out.println(String.format("%s: 这是咱创业的第一炮，一定要打响，测试不能掉链子啊，不能让APP带伤上战场，以后给你多招点人，你就是领导了...",tester.getName()));

}

}

过了一段时间，老板又觉的短视频很火，又要做短视频，这就要求给每一员工增加一套新的算法。

public class LiveApp implements CorporateSlaveVisitor {

@Override

public void visit(Programmer programmer) {

System.out.println(String.format("%s: 最近小视频很火啊，咱能不能抄袭下抖音，搞他一炮,将来公司上市了，你的身价至少也是几千万，甚至上亿...",programmer.getName()));

}

@Override

public void visit(HumanSource humanSource) {

System.out.println(String.format("%s: 咱公司就数你长得靓，哪天化化妆，把你的事业线适当露一露，要是火了你在北京买房都不是梦...",humanSource.getName()));

}

@Override

public void visit(Tester tester) {

System.out.println(String.format("%s: 你也开个账户，边测试边直播，两不耽误...",tester.getName()));

}

}

再过段时间老板可能要开KTV，程序员王二狗可能要下海当鸭子，其他两位也需要解锁新技能...

**客户端使用**

public class VisitorClient {

public void startProject(){

BigHuYouCompany bigHuYou= new BigHuYouCompany();

//可以很轻松的更换Visitor，但是要求BigHuYouCompany的结构稳定

System.out.println("-----------------启动社交APP项目--------------------");

bigHuYou.startProject(new SocialApp());

System.out.println("-----------------启动短视频APP项目--------------------");

bigHuYou.startProject(new LiveApp());

}

}

输出

\-----------------启动社交APP项目--------------------

王二狗: 给你一个月，先仿照微信搞个类似的APP出来,要能语音能发红包,将来公司上市了少不了你的，好好干...

上官无需: 咱现在缺人，你暂时就充当了陪聊吧，在程序员开发APP期间，你去发发软文，积攒点粉丝...

牛翠花: 这是咱创业的第一炮，一定要打响，测试不能掉链子啊，不能让APP带伤上战场，以后给你多招点人，你就是领导了...

\-----------------启动短视频APP项目--------------------

王二狗: 最近小视频很火啊，咱能不能抄袭下抖音，搞他一炮,将来公司上市了，你的身价至少也是几千万，甚至上亿...

上官无需: 咱公司就数你长得靓，哪天化化妆，把你的事业线适当露一露，要是火了你在北京买房都不是梦...

牛翠花: 你也开个账户，边测试边直播，两不耽误...

你看虽然大忽悠老板的需求变化这么快，但至始至终我们只是在增加新的Visitor实现类，而没有去修改任何一个Element类，这就很好的符合了开闭原则。

**访问者模式要点总结**

- 准确识别出Visitor实用的场景，如果一个对象结构不稳定决不可使用，不然在增删元素时改动将非常巨大。
- 对象结构中的元素要可以迭代访问
- Visitor里一般存在与元素个数相同的visit方法。
- 元素通过accept方法通过this将自己传递给了Visitor。

**双分派（dispatch）**

访问者模式存在一个叫"伪动态双分派”的技术，这个还是比较难懂的，访问者模式之所以是最复杂的设计模式与其有很大的关系。

什么叫分派？根据对象的类型而对方法进行的选择,就是分派(Dispatch)。

发生在编译时的分派叫静态分派，例如重载（overload），发生在运行时的分派叫动态分派，例如重写（overwrite）。

**单分派与多分派**

**单分派**

依据单个宗量进行方法的选择就叫单分派，Java 动态分派只根据方法的接收者一个宗量进行分配，所以其是单分派

**多分派**

依据多个宗量进行方法的选择就叫多分派，Java 静态分派要根据方法的接收者与参数这两个宗量进行分配，所以其是多分派

好了，理论的只是罗列出来了，那具体到访问者模式是个什么情况呢？

先看在BigHuYouCompany类里的分派代码：slave.accept(visitor); 中accept方法的分派是由slave的运行时类型决定的。若slave是Programer就执行Programer的accept方法。若slave是Tester那么就执行Tester的accept方法。

public void startProject(CorporateSlaveVisitor visitor){

for (CorporateSlave slave : employee) {

slave.accept(visitor);

}

}

**_通过此步骤就完成了一次动态单分派_**。

再看一下具体的Element里的分派代码：visitor.visit(this); 中visit方法的分派是由参数 this的运行时类型决定的。若this是Programer就执行Visitor中的visit(Programer)方法。若slave是Tester那么就执行Visitor的visit(Tester)方法。

@Override

public void accept(CorporateSlaveVisitor visitor) {

visitor.visit(this);

}

**_通过这一步又完成了一次动态单分派_**。

**两次动态单分派结合起来就完成了一次伪动态双分派**，为什么叫伪动态装派呢？因为在Java中动态分派是单分派的，而此处是通过两次动态单分派达到了双分派的效果，所以说是伪的！

**双分派机制**

**本质**：**通过两次单分派（先按调用者类型，再按参数类型），让方法执行版本由 “两个维度” 共同决定**。

双分派通过 “两次自动匹配”，替代了手动的 if-else，就像上面的代码一样。

**生活类比**

- 调用者（元素）= 快递（可能是文件、包裹、生鲜）；
- 参数（访问者）= 操作（派送、退货、暂存）；
- 第 1 次分派（找 Accept）= 快递分拣：根据快递类型（文件 / 包裹）分到对应分拣区（对应元素的 Accept 方法）；
- 第 2 次分派（找 Visit）= 执行操作：分拣区根据操作类型（派送 / 退货），对快递执行对应处理（对应访问者的 Visit 方法）；
- 最终结果：文件被派送、包裹被退货，精准匹配。

**优缺点**

**优点**

- 使得给结构稳定的对象增加新算法变得容易，提搞了代码的可维护性，可扩展性。

**缺点**

- 太复杂，特别是伪动态双分派，不仔细理解很难想清楚。

**总结**

访问者模式使用频率较低，而且复杂，可酌情掌握！