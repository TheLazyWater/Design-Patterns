**State**

**类型**：行为型

**定义**：当一个对象内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。

状态模式在日常开发中是一个非常实用的模式，可以将你的代码逼格迅速提升一个档次，所以让我们开始今天的卓越之旅吧。

**使用场景**

你发现你的代码里面存在一个很长的if else列表，而这些分支都是因为不同状态下执行的操作不一样时考虑使用此模式

**UML图**

**参与者**

- State:是一个接口，封装了状态以及其行为
- ConcreteStateX：State的实现类，表示具体的状态。
- Context：保持并切换各个状态，其持有一个State的引用。它将依赖状态的各种操作委托给不同的状态对象执行。其负责与客户端交互。

**实例**

物流系统就很适合使用状态模式来开发，因为此过程存在很多不同的状态，例如接单，出库，运输，送货，收货，评价等等。而订单在每个不同的状态下的操作可能都不一样，例如在接单状态下，商家就需要通知仓库拣货，通知用户等等操作，其他状态类似

1.  定义一个状态接口

此接口定义各个状态的统一操作接口

public interface LogisticsState {

void doAction(JdLogistics context);

}

第二个，定义一个物流Context类

此类持有一个LogisticsState 的引用，负责在流程中保持并切换状态

public class JdLogistics {

private LogisticsState logisticsState;

public void setLogisticsState(LogisticsState logisticsState) {

this.logisticsState = logisticsState;

}

public LogisticsState getLogisticsState() {

return logisticsState;

}

public void doAction(){

Objects.requireNonNull(logisticsState);

logisticsState.doAction(this);

}

}

第三步，实现各种状态类

- 接单状态类，其需要实现LogisticsState接口

public class OrderState implements LogisticsState {

@Override

public void doAction(JdLogistics context) {

System.out.println("商家已经接单，正在处理中...");

}

}

- 出库状态类

public class ProductOutState implements LogisticsState {

@Override

public void doAction(JdLogistics context) {

System.out.println("商品已经出库...");

}

}

依次类推，可以建立多个状态类

第四步，客户端的使用

public class StateClient {

public void buyKeyboard() {

//状态的保持与切换者

JdLogistics jdLogistics = new JdLogistics();

//接单状态

OrderState orderState = new OrderState();

jdLogistics.setLogisticsState(orderState);

jdLogistics.doAction();

//出库状态

ProductOutState productOutState = new ProductOutState();

jdLogistics.setLogisticsState(productOutState);

jdLogistics.doAction();

//运输状态

TransportState transportState = new TransportState();

jdLogistics.setLogisticsState(transportState);

jdLogistics.doAction();

}

}

可见，我们将每个状态下要做的具体动作封装到了每个状态类中，我们只需要切换不同的状态即可。如果不使用状态模式，我们的代码中可能会出现很长的if else列表，这样就不便于扩展和修改了。

**技术要点总结**

- 必须要有一个Context类，这个类持有State接口，负责保持并切换当前的状态。
- 状态模式没有定义在哪里进行状态转换，本例是在Context类进行的，也有人在具体的State类中转换

**当使用Context类切换状态时**，状态类之间互相不认识，他们直接的依赖关系应该由客户端负责。 例如，只有在接单状态的操作完成后才应该切换到出库状态，那么出库状态就对接单状态有了依赖，这个依赖顺序应该由客户端负责，而不是在状态内判断。

**当使用具体的State类切换时**，状态直接就可能互相认识，一个状态执行完就自动切换到了另一个状态去了

**总结**

状态模式非常适用**有限状态机(FSM)**，相信作为游戏开发者的你，日常开发中也使用过其。