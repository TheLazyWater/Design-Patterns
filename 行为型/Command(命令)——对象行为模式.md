**Command**

**类型**：行为型

**这是一个比较常用的模式**。

**定义：**将一个请求封装成一个对象，从而让用户使用不同的请求把客户端参数化；对请求排队或记录日志，以及支持可撤销的操作。

**使用场景**

- 当需要将各种执行的动作抽象出来，使用时通过不同的参数来决定执行哪个对象
- 当某个或者某些操作需要支持撤销的场景
- 当要对操作过程记录日志，以便后期通过日志将操作过程重新做一遍时
- 当某个操作需要支持事务操作的时候

UML

**参与者**

1.  Command: 是一个接口，定义一个命令
2.  ConcreteCommand：具体的执行命令，他们需要实现Command接口
3.  Receiver: 真正执行命令的角色，哪些具体的命令引用它，让它完成命令的执行
4.  **Invoker**:负责按照客户端的指令设置并执行命令，像命令的撤销，日志的记录等功能都要在此类中完成。

**实例**

21世纪，人类社会的科技日新月异，狂人马斯克正在努力在2050年前将100万人类送往火星殖民。同时人类社会在其他方面也在努力创新，比如人工智能，这不最近有一家科技公司尽然制造出了高智慧机器人，还支持订制，真是太贴心了。王二狗很兴奋，乘着媳妇出差的时候，偷偷订制了两款：BingBing号与MiMi号。

今夜，王二狗就是整个小区那个最靓的仔，一切准备就绪，准备发车... 二狗随后会向机器人发出各种各样的指令，这个就很适合使用命令模式

第一步：声明一个命令接口(Command)

/\*\*

\* 命令接口，所有具体的命令都会实现此接口，**Invoker只认识此接口**

\*

\* 其实现类自己包含了可以执行自己的对象（receiver）,已经执行时候需要的数据

\*/

public interface Command {

void execute();

}

第二步: 构建那些可以具体完成命令的角色(Receiver)

构建一个BingBing号机器人

public class BingBingReceiver {

public void singSong(){

System.out.println("落花满天蔽月光 借一杯附荐凤台上...");

}

public void playPiano(){

System.out.println("随着BingBing纤细的双手在琴弦上来回撩拨，美妙的音乐响彻了整个房间...");

}

}

构建一个MIMI号机器人

public class MiMiReceiver {

public void hotDance(){

System.out.println("终日以文雅示人的大MIMI尽然也有她狂野的一面，只见她随着音乐疯狂的扭动，魅惑的表情更是让人想入非非，欲罢不能...");

}

}

第三步:构建各种具体命令(ConcreteCommand)

构建一个唱歌命令，其要实现Command接口。因为只有BingBing机器人提供这个功能，所以我们要在这个命令内部使用BingBingReceiver 来具体执行。

public class SingSongCommand implements Command {

private BingBingReceiver bingBing;

public SingSongCommand(BingBingReceiver bingBing) {

this.bingBing = bingBing;

}

@Override

public void execute() {

bingBing.singSong();

}

}

构建一个弹琴命令，与前面唱歌命令类似

public class PlayPianoCommand implements Command {

private BingBingReceiver bingBing;

public PlayPianoCommand(BingBingReceiver bingBing) {

this.bingBing = bingBing;

}

@Override

public void execute() {

bingBing.playPiano();

}

}

二狗还想看辣舞，所以再构建一个跳辣舞命令。由于只有MiMi号机器人提供这个能力，所以此处的执行者就变成了MiMiReceiver

public class TiaoLaWuCommand implements Command {

private YangMiReceiver daMiMi;

private String duration;//跳舞的时长

public TiaoLaWuCommand(YangMiReceiver daMiMi, String duration) {

this.daMiMi = daMiMi;

this.duration = duration;

}

@Override

public void execute() {

System.out.println(String.format("开始跳舞表演，时长为：%s", duration));

daMiMi.hotDance();

}

}

我们看到，此命令里面除了具体执行命令的MiMiReceiver，还包含了命令执行时需要的数据，例如跳舞时长。这也是命令模式需要注意的地方，具体的命令类里不止包含具体执行命令的那个对象，也包含相关数据。

**第四步：构建命令的调用者 （Invoker）**

机器人和命令都准备好了，那么具体怎么发送呢？这就是Invoker的角色了。二狗买这两个机器人时还带了一个智能语音控制器，他通过这个控制器就可以发出各种命令了

public class RobotInvoker {

private final List&lt;Command&gt; sexRobotCommands = new ArrayList<>();

public void clearCommand(){

sexRobotCommands.clear();

}

//设置一套命令，不知道具体执行者是谁

public void addCommands(Command command) {

sexRobotCommands.add(command);

}

//执行系列命令

public void executeCommand() {

for (Command command : sexRobotCommands) {

command.execute();

}

}

}

第五步：客户端的使用

终于到了发车的时刻了。二狗兴奋的唤起了控制系统（RobotInvoker ）喊道：来来，让bingbing先把琴弹起来，再把歌唱起来... 还有，让mimi来段辣舞，要那种让人欲罢不能的辣舞哦...

public class DogWang2Client {

//享受陪伴机器人的服务

public void enjoySexRobot() {

//robot 控制系统，用户通过此系统来发出命令

RobotInvoker invoker = new RobotInvoker();

//生成唱歌弹琴命令

BingBingReceiver bingBingReceiver = new BingBingReceiver();

SingSongCommand singSongCommand = new SingSongCommand(bingBingReceiver);

PlayPianoCommand playPianoCommand = new PlayPianoCommand(bingBingReceiver);

//构建执行计划

invoker.addCommands(singSongCommand);

invoker.addCommands(playPianoCommand);

//执行命令

invoker.executeCommand();

//生成跳舞命令

...

//执行命令

invoker.executeCommand();

}

}

**命令模式的要点如下：**

1.  Command 接口非常简单，通常只有一个execute方法，如果要支持撤销操作的话，再加一个unexecute方法
2.  每个具体的命令类内部封装了实际执行命令的那个类（Recevier），或者那些类，以及执行需要的数据
3.  每个具体命令类只完成一个请求，有多少个请求就有多少个命令
4.  Invoker类只认识接口Command，其他的都不认识
5.  客户端类负责生成命令，并通过Invoker组装执行。

**优缺点**

**优点**

- **将调用操作与具体执行者解耦**：你只管发出命令，至于命令由谁执行你不用关心。我们也可以随时将命令中具体执行者换掉，发出命令者是不知道的。例如你大老板交给经理一个任务，至于经理安排小张，还是小王来做，他根本就不关心
    - 添加一个命令非常容易
    - 很容易实现序列操作及实现回调系统

你把命令加到一个列表中，迭代执行就可以实现序列操作了。 因为Java不能将函数作为参数，此处我们可以将命令对象当做参数，而这个对象还可执行，所以就实现了回调功能。

**缺点**

- 类太多，每次增加一个命令，就要多加一个类。

**总结**

**总的来说此模式在日常开发中使用频率不高，但关键时刻是能起大作用的**