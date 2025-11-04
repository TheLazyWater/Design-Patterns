**Flyweight**

**类型**：结构型

也许你会觉得享元模式比较陌生，但是相信在你的软件开发生涯中应该不知不觉的用了很多次，只是你没有总结。例例如你肯定用到过缓存，用到过对象池...

不知道作为IT猿的你听说过**麦克斯韦**这个人不，这个人可牛逼了，就是他提出了著名的麦克斯韦方程式。但是你知道吗，这个方程式的东西都没有一个是他研究出来的，但他很好的把前人的研究总结形成了一个体系，这一下就牛逼了，足见总结的重要性。

**定义**：允许使用对象共享来有效地支持大量细粒度对象

**使用场景**：当你的程序中存在大量相似对象，每个对象之间只是根据不同的使用场景有些许变化时。

**UML类图**

**参与者**

1.  Flyweight: 享元接口，定义所有对象共享的操作
2.  ConcreteFlyweight：具体的要被共享的对象，其一般是一个不可变类，内部只保存需要共享的内部状态，它可能不止一个。
3.  FlyweightFactory: 负责给客户端提供共享对象

其实经典的享元模式还有一个UnshareConcreteFlyweight的角色，它也实现Flyweight接口。它的作用是当你不需要共享对象时，但是又需要以统一接口处理此对象，就可以添加这个角色。

共享对象的状态分为**内部状态**与**外部状态，内部状态在各个对象间共享，而外部状态由客户端传入**，这一点一定要牢记。

**实例**

假设你要设计一个五子棋，通过分析，你发现。这里需要大量的棋子对象，它们除了颜色有黑白之分，摆放的位置不同其他都一样，非常适合使用享元模式。

**第一步，定义一个共享对象通用的接口**

我们的棋子对象有一个绘制自己的**通用**操作

public interface Chess {

//绘制棋子

void draw(int x,int y);

}

**第二步，实现需要共享的对象类**

我们的棋子对象分为黑白两类，所以此处我们将颜色设计为对象的内部状态来共享，而不是外部状态，所以就需要黑白两个对象类。如果你把颜色作为外部状态，那么只需要一个对象类即可。

//黑棋

public class BlackChess implements Chess {

//内部状态，共享

private final Color color = Color.BLACK;

private final String sharp = "圆形";

public Color getColor() {

return color;

}

@Override

public void draw(int x, int y) {

System.out.println(String.format("%s%s棋子置于（%d，%d）处", sharp, color.getAlias(), x, y));

}

}

//白棋

public class WhiteChess implements Chess {

//内部状态，共享

private final Color color = Color.WHITE;

private final String sharp = "圆形";

public Color getColor() {

return color;

}

@Override

public void draw(int x, int y) {

System.out.println(String.format("%s%s棋子置于（%d，%d）处", sharp, color.getAlias(), x, y));

}

}

**第三步，共享对象工厂**

其负责提供共享对象，客户端不应该直接实例化棋子对象，而应该使用此工厂来获取。因为我们分了黑白两类对象，所以这里使用Color为key的map来存储共享对象。

public class ChessFactory {

private static final Map&lt;Color, Chess&gt; chessMap = new HashMap<>();

public static Chess getChess(Color color) {

Chess chess = chessMap.get(color);

if (chess == null) {

chess = color == Color.WHITE ? new WhiteChess() : new BlackChess();

chessMap.put(color, chess);

}

return chess;

}

}

**第四步，客户端使用**

public class FlyweightClient {

public void playChess() {

//下黑子

Chess backChess1 = ChessFactory.getChess(Color.BLACK);

backChess1.draw(2, 5);

//下白子

Chess whiteChess = ChessFactory.getChess(Color.WHITE);

whiteChess.draw(3, 5);

//下黑子

Chess backChess2 = ChessFactory.getChess(Color.BLACK);

backChess2.draw(2, 6);

System.out.println(String.format("backChess1:%d | backChess2:%d | whiteChess:%d",

backChess1.hashCode(), backChess2.hashCode(), whiteChess.hashCode()));

}

}

**输出：**

圆形黑色棋子置于（2，5）处

圆形白色棋子置于（3，5）处

圆形黑色棋子置于（2，6）处

backChess1:1867083167 | backChess2:1867083167 | whiteChess:1915910607

从输出可见，**backChess1 与 backChess2 是同一个对象**，而whiteChess是另一个不同的对象。所以不论棋盘上有多少颗棋子，程序中只会保持最多两个棋子对象，这就极大的节约了内存。

**技术要点总结**

1.  首先一定要区分出内部状态与外部状态，共享对象只持有内部状态，内部状态不可以从客户端设置，而外部状态必须从客户端设置。
2.  合理设计共享对象分类，大部分情况下会设计成一组，而不是一个共享对象。
3.  共享对象要求是不可变对象，从FlyWeightFactory获取到的对象都是一个原始的对象，而不是一个状态不确定的对象。