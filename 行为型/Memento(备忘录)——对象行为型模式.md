**Memento**

**类型**：行为型

**定义**：在不破坏封闭的前提下捕获一个对象的内部状态，并在该对象之外保存这个状态，从而可以将对象恢复到原先保存的状态

**使用场景**

当你正在开发一个功能，这个功能需要存档的时候就应该想到它。例如游戏，文档编辑器等等，都需要在你下次重新打开的时候恢复到你关闭它时候的状态。

**UML图**

**参与者**

Originator: 我们知道备忘录模式就是要完成保存状态，然后恢复状态的功能。那么保存和恢复谁的状态呢？对了，就是这个角色的状态。

Memento：这个就比较简单了，就是一个存储状态的类，里面没有业务逻辑，一般是一个POJO。

CareTaker：负责保存和恢复Originator的状态，状态是保存在这类里面的。

**实例**

现在我们要保存一个游戏的记录

第一步：定义Originator

public class GameOriginator {

private int currentScore;

//将需要保存的状态分装在Memento里对外提供

public [GameProgressMemento](https://zhida.zhihu.com/search?content_id=171876536&content_type=Article&match_order=1&q=GameProgressMemento&zhida_source=entity) saveProcess() {

return new GameProgressMemento(currentScore);

}

//通过从外部接收的Memento恢复状态

public void restoreProcess(GameProgressMemento memento) {

currentScore = memento.getScore();

}

//对内部状态的使用

public void playGame() {

System.out.println("------------------开始游戏------------------");

System.out.println("当前分数为："+ currentScore);

System.out.println("杀死一个小怪物得1分");

currentScore++;

System.out.println(String.format("总分为：%d", currentScore));

}

public void exitGame(){

System.out.println("退出游戏");

currentScore=0;

System.out.println("-----------------退出游戏-------------------");

}

}

第二步：构建备忘录Memento

这个类最简单，其基本上就是一个POJO( **不依赖任何特定框架、没有继承特定类或实现特定接口的简单 Java 对象**)。它不包含业务逻辑，只包含状态数据，结构由要保存的状态类决定。例如我们这里只保存一个内部状态，游戏分数。

public class GameProgressMemento {

private int score;

public GameProgressMemento(int score) {

this.score = score;

}

public int getScore() {

return score;

}

}

第三步:构建CareTaker

如果说备忘录模式有一点点技巧的话，也就是这个类了。CareTaker相对于Originator来说是一个外部组件，它帮助Originator保存了状态，相当于Originator将自己某一个时刻的状态保存到了外部。

当我们要保存状态时，使用此类的saveMemento。当我们要恢复状态时，使用此类的getMemento

public class [GameCareTaker](https://zhida.zhihu.com/search?content_id=171876536&content_type=Article&match_order=1&q=GameCareTaker&zhida_source=entity) {

private List&lt;GameProgressMemento&gt; memento= new ArrayList<>();

public void saveMemento(GameProgressMemento memento) {

this.memento.add(memento);

}

public GameProgressMemento getMemento(int index) {

return this.memento.get(index);

}

}

第四步：客户端的使用

public class MementoClient {

public void replayGame() {

GameOriginator originator = new GameOriginator();

GameCareTaker careTaker = new GameCareTaker();

//玩游戏

originator.playGame();

//保存进度

careTaker.saveMemento(originator.saveProcess());

//退出游戏

originator.exitGame();

//重新打开游戏，恢复进度

originator.restoreProcess(careTaker.getMemento(0));

originator.playGame();

}

}

输出结果

\------------------开始游戏------------------

当前分数为：0

杀死一个小怪物得1分

总分为：1

退出游戏

\-----------------退出游戏-------------------

\------------------开始游戏------------------

当前分数为：1

杀死一个小怪物得1分

总分为：2