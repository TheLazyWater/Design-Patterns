**Mediator**

**类型**：行为型号

**定义**：通过一个 “中介者” 对象，集中处理多个对象（同事）之间的交互，使原本相互依赖的同事类解耦，不再直接通信。

**使用场景**：

假设你开发一个即时聊天软件，有多个用户（UserA、UserB、UserC），用户之间需要互发消息。如果不用中介者模式，每个用户都要持有其他所有用户的引用（比如 UserA 要直接调用 UserB.Receive()、UserC.Receive()），形成如下**网状依赖**：

中介者模式通过引入 “聊天室（中介者）”，将网状依赖转为**星状依赖**：所有用户只和聊天室交互，用户之间互不感知，结构如下：

**参与者**

1.  抽象中介者：定义中介者的接口，声明 “注册同事” 和 “协调同事交互” 的方法（如 Register(Colleague)、SendMessage(Colleague, string)）。
2.  具体中介者：实现抽象中介者接口，持有所有同事的引用，负责实际的协调逻辑（如将消息从一个同事转发给其他同事）。
3.  抽象同事类：定义同事类的接口，持有抽象中介者的引用（所有同事都知道中介者），声明 “发送消息” 和 “接收消息” 的方法。
4.  具体同事类：实现抽象同事类接口，发送消息时不直接找其他同事，而是通过中介者（mediator.SendMessage(this, message)），接收消息时处理自身逻辑。

**案例**

以 “多用户通过聊天室互发消息” 为例，用中介者模式实现：

第一步：定义抽象中介者和抽象同事类

using System;

using System.Collections.Generic;

// 1. 抽象中介者：聊天室接口

public interface IChatRoomMediator {

// 注册同事（用户）

void Register(User user);

// 转发消息（从发送者到其他用户）

void SendMessage(User sender, string message);

}

// 2. 抽象同事类：用户接口

public abstract class User {

protected IChatRoomMediator \_mediator; // 持有中介者引用

public string Name { get; }

public User(IChatRoomMediator mediator, string name) {

\_mediator = mediator;

Name = name;

}

// 发送消息（通过中介者）

public abstract void Send(string message);

// 接收消息（处理自身逻辑）

public abstract void Receive(string senderName, string message);

}

第二步：实现具体中介者(聊天室)

// 具体中介者：实现聊天室逻辑

public class ChatRoom : IChatRoomMediator {

// 保存所有注册的用户（同事）

private List&lt;User&gt; \_users = new List&lt;User&gt;();

// 注册用户

public void Register(User user) {

\_users.Add(user);

// 可选：用户加入时通知其他人

SendMessage(null, $"{user.Name} 加入了聊天室");

}

// 转发消息：给除了发送者之外的所有用户

public void SendMessage(User sender, string message) {

foreach (var user in \_users) {

// 不发给自己

if (sender == null || user != sender) {

user.Receive(sender?.Name ?? "系统", message);

}

}

}

}

第三步：实现具体同事类(聊天用户)

// 具体同事类：聊天用户

public class ChatUser : User {

public ChatUser(IChatRoomMediator mediator, string name)

: base(mediator, name) { }

// 发送消息：调用中介者转发

public override void Send(string message) {

Console.WriteLine($"\\n{Name} 发送消息：{message}");

\_mediator.SendMessage(this, message); // 核心：通过中介者发送

}

// 接收消息：处理自己的逻辑

public override void Receive(string senderName, string message) {

Console.WriteLine($"{Name} 收到 {senderName} 的消息：{message}");

}

}

第四步：客户端

class Program {

static void Main(string\[\] args) {

// 创建中介者（聊天室）

IChatRoomMediator chatRoom = new ChatRoom();

// 创建同事（用户），并注册到聊天室

User alice = new ChatUser(chatRoom, "Alice");

User bob = new ChatUser(chatRoom, "Bob");

User charlie = new ChatUser(chatRoom, "Charlie");

chatRoom.Register(alice);

chatRoom.Register(bob);

chatRoom.Register(charlie);

// 用户发送消息（通过中介者转发）

alice.Send("大家好！");

/\* 输出：

Alice 发送消息：大家好！

Bob 收到 Alice 的消息：大家好！

Charlie 收到 Alice 的消息：大家好！

\*/

bob.Send("Alice 你好！");

/\* 输出：

Bob 发送消息：Alice 你好！

Alice 收到 Bob 的消息：Alice 你好！

Charlie 收到 Bob 的消息：Alice 你好！

\*/

}

}

**适用场景**

1.  **对象间交互频繁且复杂**：如多人聊天、多人协作工具（如腾讯文档多人编辑）、电商平台的订单 - 库存 - 支付系统（订单创建需通知库存扣减、支付验证，通过中介者协调）。
2.  **系统中存在 “网状依赖”**：对象间相互引用，修改一个对象需牵动多个对象，用中介者转为 “星状依赖”。
3.  **需要集中管理交互逻辑**：如机场塔台（中介者）协调多架飞机（同事）的起飞降落，避免飞机直接通信。

**总结**

中介者模式的核心是 “**通过中介者集中处理对象间的交互，将多对多依赖转为一对多**”。它特别适合解决对象间交互频繁导致的耦合问题，让系统结构更清晰、更易维护。