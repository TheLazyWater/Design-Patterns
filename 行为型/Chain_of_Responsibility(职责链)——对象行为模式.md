**Chain of Responsibility 职责链**

**类型**：行为型

责任链模式是一个**相对比较简单的模式**，它的名字已经非常好的暗示了其工作原理。**每个处理器互相首尾连接在一起成为一条链**，**然后任务顺着这条链往下传，直到被某个处理器处理掉**。

**定义：**避免请求发送者与接收者耦合在一起，让多个对象都有可能接收请求，将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。

**使用场景**

一个请求需要被多个对象中的某一个处理，但是到底是哪个对象必须在运行时根据条件决定。

**UML图**

责任链模式有两个角色

1.  Handler：所有处理器类的接口
2.  ConcreteHandler:具体的处理类，其实现Handler接口，有多少个处理器，就定义多少个这样的类

这个责任链模式唯一的难点就是**怎么将很多处理器对象连成一条链**，我们来看代码

**实例**

你有一笔预算申请，但是你不知道会由哪层领导处理，而各层领导的审批职责就好像铁链一样连接在一起，一个预算请求沿着这条链一直往上传... 让我们用代码来实现上面的场景吧。

1.  设计一个所有处理器都要实现的接口

public interface BudgetHandler {

void setNextHandler(BudgetHandler nextHandler);

boolean handle(int amount);

}

其中setNextHandler(BudgetHandler)方法负责设置下一个处理器，以便在自己不能处理此请求的情况下，将请求交给下一个处理器。handle(int) 方法负责处理请求。

1.  实现各种处理器

**小组领导类**

public class GroupLeader implements BudgetHandler {

private BudgetHandler nextHandler;

@Override

public void setNextHandler(BudgetHandler nextHandler) {

this.nextHandler = nextHandler;

}

@Override

public boolean handle(int amount) {

Objects.requireNonNull(nextHandler);

if(amount<1000){

System.out.println("小钱，批了！");

return true;

}

System.out.println(String.format("%d超出GroupLeader权限,请更高级管理层批复",amount));

return nextHandler.handle(amount);

}

}

**经理类**

public class Manager implements BudgetHandler {

private BudgetHandler nextHandler;

@Override

public void setNextHandler(BudgetHandler nextHandler) {

this.nextHandler = nextHandler;

}

@Override

public boolean handle(int amount) {

Objects.requireNonNull(nextHandler);

if(amount<5000){

System.out.println("小于2000块，我这个经理可以决定：同意！");

return true;

}

System.out.println(String.format("%d超出Manager权限,请更高级管理层批复",amount));

return nextHandler.handle(amount);

}

}

**首席财务官类**

public class CFO implements BudgetHandler {

private BudgetHandler nextHandler;

@Override

public void setNextHandler(BudgetHandler nextHandler) {

this.nextHandler = nextHandler;

}

@Override

public boolean handle(int amount) {

if(amount<50000){

System.out.println("CFO同意,希望你再接再厉，为公司做出更大的贡献。");

return true;

}

if (nextHandler!=null){

return nextHandler.handle(amount);

}

//已经没有更高级的管理层来处理了

System.out.println(String.format("%d太多了，回去好好看看能不能缩减一下",amount));

return false;

}

}

第三步:客户端

每个处理器都建好了，那么**怎么才能让他们连成链呢？这就是客户端的责任了**

public class DogWang2Cor {

public void applyBudget() {

GroupLeader leader = new GroupLeader();

Manager manager = new Manager();

CFO cfo = new CFO();

leader.setNextHandler(manager);

manager.setNextHandler(cfo);

System.out.println(String.format("领导您好：由于开发需求，需要购买一台Mac笔记本电脑，预算为%d 望领导批准", 95000));

if (leader.handle(95000)) {

System.out.println("谢谢领导");

} else {

System.out.println("巧妇难为无米之炊，只能划船了...");

}

}

}

第四步:输出结果

领导您好：由于开发需求，需要购买一台Mac笔记本电脑，预算为20000 望领导批准

20000超出GroupLeader权限,请更高级管理层批复

20000超出Manager权限,请更高级管理层批复

CFO同意,希望你再接再厉，为公司做出更大的贡献。

谢谢领导

**责任链模式要点**

- Handler 接口持有它自己的类型，通过set方法或者构造函数将责任链上的下一个处理器赋值进去
- 客户端负责将各个处理器连成链，而且必然知道链上的第一个处理器，通过调用它的handle方法触发处理流程
- 注意千万不能将链搞成一个环（将最后一个处理的下一个handler设置为第一个），那样就无法结束了。