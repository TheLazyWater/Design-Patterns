**Façade Pattern**

**类型**：结构型

Facade Pattern 有时也翻译成面板模式，**是一个使用频率极高的设计模式**。思想非常简单，对外提供简单的交互接口，隐藏内部的复杂性。

这在现实世界实在是太常见了，只要世界发生了混乱，这个模式就会有用武之地。此种机会的精髓就是： **乱**！一般是行业初期多家服务商各种竞争还谁都没有胜出的时候，等到了后期决出老大后你这个生意也就凉了，如果一直决不出，那就可以一直做。

例如移动广告最开始发展时，市面上做移动广告的少说也有几十上百家，关键是由于没有决出行业龙头，好多家实力差不多，用户就面临要一个一个接入这些广告sdk的尴尬，那如果此时有一家公司出个统一的SDK聚合一下这些商家，用户只需要与这一家对接多好，你是不是觉得你找到了个一本万利的创业机会了，不好意思你能想到的会外观设计模式的人早就这么做了，所以说设计模式重要吧...

**定义**：提供一个高层次的接口，使得子系统更易于使用

**使用场景**：

**当你的程序有很多个模块，或者说子系统。你希望给用户提供一个统一的操作界面类，而不是让用户分别与这些模块交互**。

一种设计是：给用户提供一套面板方用来法完成一般性的功能，如果面板提供的方法不能满足用户需求，用户还可以直接使用具体模块的功能来达到自己的目的。 另一种设计是：用户完全看不到各子模块，所有操作必须通过面板来完成，如果面板没有提供相应的功能，那也就gg了。

**UML图**

从上图可见，外观模式极其简单，只包含两个组成部分

|     |     |
| --- | --- |
| Façade | 就这个一个关键部分，里面引用各个子模块，然后对外提供统一的接口 |
| 其他模块 | 要被隐藏的哪些独立功能模块，或者说是子系统 |

**实例**

**第一步：确定需要调用的子模块系统**

确定我们要使用面板隐藏的子模块，此处假设有3个子模块。

//订单系统

public class OrderSys {

public String getOrderNum(){

System.out.println("获取订单号");

return "123456";

}

}

//支付系统

public class PaymentSys {

private OrderSys orderSys;

public PaymentSys(OrderSys orderSys) {

this.orderSys = orderSys;

}

public BigDecimal getOrderAccount(String orderNum){

System.out.println(String.format("获取%s订单支付金额",orderNum));

return BigDecimal.valueOf(500);

}

}

//物流系统

public class DeliverySys {

public int getDeliveryTime(){

System.out.println("获取配送耗时");

return 30\*60;//30分钟

}

}

**第二步，创建外观类**

这个模式实在是太简单了，基本没什么可说的。这个外观类就是引用其他功能模块，完成相关的功能对外暴露操作接口。

例如本例中的外观类ReportFacade使用了OrderSys、PaymentSys与DeliverySys 3个子模块来完成一个生成报表的功能，客户端只需要通过此方法就可以生成报表了，不用关心那些子模块。

public class ReportFacade {

public void generateReport() {

OrderSys orderSys = new OrderSys();

PaymentSys paymentSys = new PaymentSys(orderSys);

DeliverySys deliverySys = new DeliverySys();

final String orderNum = orderSys.getOrderNum();

System.out.println(String.format("\\n报表\\n--------------------------------------------\\n" +

"订单号：%s | 金额：%s元 | 配送耗时：%s分钟",

orderNum,

paymentSys.getOrderAccount(orderNum).toPlainString(),

String.valueOf(deliverySys.getDeliveryTime() / 60))

);

}

}

**第三步：客户端的使用**

public class FacadeClient {

public void printReport(){

new ReportFacade().generateReport();

}

}

可见客户端只用外观类交互，完全不用与具体的子模块交互。

**技术要点总结**

过于简单基本没啥说的，唯一注意的一点是大部分的外观模式没有限制客户端直接使用子系统。也就是说如果用户遇到了通过外观方法无法完成的功能，可以自己调用相关子系统去完成。

**总结：**

这个模式很简单！