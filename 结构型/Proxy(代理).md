**Proxy代理模式**

**类型**：结构型

**定义**：为其他对象提供一种代理以控制对这个对象的访问

**实例**：

二狗公司（天津在线回声科技发展有限公司）老板突然在发工资的前一天带着小姨子跑路了，可怜二狗一身房贷，被迫提起劳动仲裁，劳动局就会为其指派一位**代理律师**全权负责二狗的仲裁事宜。那这里面就是使用了**代理模式**，因为在劳动仲裁这个活动中，代理律师会全权代理王二狗。

**使用场景**：

下面是一些使用场景，不过太抽象，暂时可以不要在意，随着你的不断进步你终究会明白的。

- **远程代理** ：为位于两个不同地址空间对象的访问提供了一种实现机制，可以将一些消耗资源较多的对象和操作移至性能更好的计算机上，提高系统的整体运行效率。
- **虚拟代理**：通过一个消耗资源较少的对象来代表一个消耗资源较多的对象，可以在一定程度上节省系统的运行开销。
- **缓冲代理**：为某一个操作的结果提供临时的缓存存储空间，以便在后续使用中能够共享这些结果，优化系统性能，缩短执行时间。
- **保护代理**：可以控制对一个对象的访问权限，为不同用户提供不同级别的使用权限。
- **智能引用**：要为一个对象的访问（引用）提供一些额外的操作时可以使用

**UML图**

**什么是静态代理**

静态代理是指预先确定了代理与被代理者的关系，例如王二狗的代理律师方文镜是在开庭前就确定的了。那映射到编程领域的话，就是指**代理类与被代理类的依赖关系在编译期间就确定了**。下面就是王二狗劳动仲裁的代码实现：

首先定义一个代表诉讼的接口

public interface ILawSuit {

void submit(String proof);//提起诉讼

void defend();//法庭辩护

}

王二狗诉讼类型，实现ILawSuit接口

public class SecondDogWang implements ILawSuit {

@Override

public void submit(String proof) {

System.out.println(String.format("老板欠薪跑路，证据如下：%s",proof));

}

@Override

public void defend() {

System.out.println(String.format("铁证如山，%s还钱","马旭"));

}

}

代理律师诉讼类，实现ILawSuit接口

public class ProxyLawyer implements ILawSuit {

ILawSuit plaintiff;//持有要代理的那个对象

public ProxyLawyer(ILawSuit plaintiff) {

this.plaintiff=plaintiff;

}

@Override

public void submit(String proof) {

plaintiff.submit(proof);

}

@Override

public void defend() {

plaintiff.defend();

}

}

产生代理对象的静态代理工厂类

public class ProxyFactory {

public static ILawSuit getProxy(){

return new ProxyLawyer(new SecondDogWang());

}

}

这样就基本构建了静态代理关系了，然后在客户端就可以使用代理对象来进行操作了

public static void main(String\[\] args) {

ProxyFactory.getProxy().submit("工资流水在此");

ProxyFactory.getProxy().defend();

}

可以看到，代理律师全权代理了王二狗的本次诉讼活动。那使用这种代理模式有什么好处呢，我们为什么不直接让王二狗直接完成本次诉讼呢？现实中的情况比较复杂，但是我可以简单列出几条：这样代理律师就可以在提起诉讼等操作之前做一些校验工作，或者记录工作。例如二狗提供的资料，律师可以选择的移交给法庭而不是全部等等操作，就是说可以对代理的对做一些控制。例如二狗不能出席法庭，代理律师可以代为出席。。。

**什么是动态代理**

动态代理本质上仍然是代理，情况与上面介绍的完全一样，只是代理与被代理人的关系是动态确定的，例如王二狗的同事牛翠花开庭前没有确定她的代理律师，而是在开庭当天当庭选择了一个律师，映射到编程领域为这个**关系是在运行时确定的**。

那既然动态代理没有为我们增强代理方面的任何功能，那我们为什么还要用动态代理呢，静态代理不是挺好的吗？凡是动态确定的东西大概都具有灵活性，强扩展的优势。上面的例子中如果牛翠花也使用静态代理的话，那么就需要再添加两个类。一个是牛翠花诉讼类，一个是牛翠花的代理律师类，还的在代理静态工厂中添加一个方法。而如果使用动态代理的话，就只需要生成一个诉讼类就可以了，全程只需要一个代理律师类，因为我们可以动态的将很多人的案子交给这个律师来处理。

静态代理的问题很明显：**每一个需要代理的类，都要手动写一个对应的代理类**。动态代理的核心逻辑是：**不用手动写代理类，在程序运行时 “自动生成” 代理类**，所有代理逻辑（如日志）集中在一个地方处理。

C# 中实现动态代理主要依赖**反射**或**IL 代码生成**（如通过 System.Reflection.Emit 动态生成类），但手动实现复杂。实际开发中常用**第三方库**简化操作，最流行的是 **Castle DynamicProxy**。

**第一步：安装Castle DynamicProxy**

通过 NuGet 安装 Castle.Core（DynamicProxy 是其一部分）：

Install-Package Castle.Core //base系统

第二步：用 DynamicProxy 实现动态代理（统一日志处理）

using System;

using Castle.DynamicProxy;

// 1. 抽象主题

public interface IUserService {

void AddUser(string username);

}

// 2. 真实主题

public class UserService : IUserService {

public void AddUser(string username) {

Console.WriteLine($"数据库中添加用户：{username}");

}

}

// 3. 定义拦截器（动态代理的核心：统一处理额外逻辑）

public class LogInterceptor : IInterceptor {

// 拦截方法调用：在真实方法执行前后添加逻辑

public void Intercept(IInvocation invocation) {

// 调用前：日志记录

Console.WriteLine($"\[动态日志\] 开始调用方法：{invocation.Method.Name}，参数：{string.Join(",", invocation.Arguments)}");

// 执行真实主题的方法

invocation.Proceed();

// 调用后：日志记录

Console.WriteLine($"\[动态日志\] 方法调用完成：{invocation.Method.Name}");

}

}

// 客户端使用动态代理

class Program {

static void Main(string\[\] args) {

// 创建代理生成器

var generator = new ProxyGenerator();

// 创建拦截器（包含额外逻辑）

var interceptor = new LogInterceptor();

// 动态生成代理类并创建实例（代理实现IUserService，包装UserService）

IUserService proxy = generator.CreateInterfaceProxyWithTarget(

new UserService(), // 真实主题实例

interceptor // 拦截器（添加额外逻辑）

);

// 调用代理方法，自动触发拦截器逻辑

proxy.AddUser("李四");

/\* 输出：

\[动态日志\] 开始调用方法：AddUser，参数：李四

数据库中添加用户：李四

\[动态日志\] 方法调用完成：AddUser

\*/

}

}

**动态代理的核心逻辑**

- **拦截器（Interceptor）**：实现 IInterceptor 接口，Intercept 方法中定义 “调用前”“调用后” 的额外逻辑（如日志、权限验证）。把所有额外逻辑（如日志）写在拦截器里，这是动态代理的 “灵魂”。拦截器会在 “调用真实方法前” 和 “调用后” 自动执行。
- **代理生成器（ProxyGenerator）**：在运行时动态生成代理类，该类实现抽象主题接口，并在方法调用时自动触发拦截器。
- **无侵入性**：真实主题和抽象主题无需修改，所有额外逻辑集中在拦截器中，可复用（如用同一个拦截器代理多个接口）。

**总结**：

静态代理比动态代理更符合OOP原则，在日常开发中使用也较多。动态代理在开发框架时使用较多，例如大名鼎鼎的Spring。

代理模式的核心是 “**找个中间层帮你做事，同时中间层还能额外处理一些琐事**”。例如：

- **租房中介**：你（客户端）想租房，不用直接找房东（真实对象），而是通过中介（代理）。中介会帮你筛选房源（额外逻辑）、带看（控制访问），最后你和房东签合同（核心逻辑）。
- **代购**：你（客户端）想买国外商品（真实对象的功能），通过代购（代理）。代购帮你下单、过关（额外逻辑），最后把商品给你（核心功能）。
- **明星经纪人**：商家（客户端）想找明星（真实对象）代言，通过经纪人（代理）。经纪人审核合同（权限验证）、安排时间（控制访问），最后明星完成代言（核心功能）。

对应到编程中，代理模式的核心价值是：**在不修改原对象代码的前提下，给原对象的功能 “套一层”，添加额外逻辑（如日志、权限、缓存等），同时不影响客户端对原对象的使用**。

**总之，动态代理可能理解起来会比较复杂，需要时间去理解和实践！！！**