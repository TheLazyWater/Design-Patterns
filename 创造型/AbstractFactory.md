**AbstractFactory**

**类型**：创造性模式

**定义：**抽象工厂为创建一组相关或者是相互依赖的对象提供一个接口，而不需要指定他们的具体类

“产品族” 指的是**_一组功能上相互配合、设计风格统一的产品_**，它们必须一起使用才能发挥完整作用，不能随意混搭。

客户端在创建产品时，不需要写死具体产品的类名（如new WindowsButton()），只需要通过“抽象工厂”来获取产品。

**参与者：**

|     |     |
| --- | --- |
| AbstractFactory | 定义创建一系列相关产品的接口 |
| ConcreteFactory | 实现抽象工厂接口，负责创建同一产品系列的具体产品 |
| AbstractProduct | 定义某类产品的接口 |
| ConcreteProduct | 实现抽象产品接口，是具体产品 |
| Client | 使用由AbstractFactory和AbstractProduct类声明的接口 |

**使用场景：**

例如小米公司和苹果公司就是两个不同产品家族，而他们两家都生产笔记本电脑和手机，那么小米的笔记本电脑和苹果的笔记本电脑肯定不一样，手机情况也是如此。这就构成了两个产品家族的系列产品之间比较的关系。

再比如麦当劳和肯德基是两个不同的产品家族，他们都生产汉堡和果汁。

**UML**

还是以小米公司和苹果公司为例，每个产品家族中包含两种产品，笔记本电脑与手机，所以需要两个工厂。

- AbstractFactory

抽象工厂接口，里面会声明生产品牌家族产品的几个方法，例如这里要生产笔记本电脑和手机，就会有这么两个方法声明。

- XiaoMiFactory

小米产品工厂，实现抽象工厂接口AbstractFactory，生产MiComputer和MiPhone。

- AppleFactory

苹果产品工厂，实现抽象工厂接口AbstractFactory，生产MacComputer和IPhone。

其中MiComputer和MacComputer是同一类型的产品，都是笔记本电脑，实现同一个接口Computer。MiPhone和IPhone是同一类型的产品，都是手机，实现同一个接口MobilePhone。

**案例(Java)：**

现在你是一名UI设计师，你需要为不同系统的UI设计不同的风格，现在给出了Windows和Mac，需要你调整他们的Button与Input的风格。

1.  定义抽象产品

// 抽象产品：按钮

public interface Button {

void render(); // 渲染按钮

}

// 抽象产品：输入框

public interface Input {

void render(); // 渲染输入框

}

1.  实现具体产品

// 具体产品：Windows按钮

public class WindowsButton implements Button {

@Override

public void render() {

System.out.println("渲染Windows风格按钮");

}

}

// 具体产品：Mac按钮

public class MacButton implements Button {

@Override

public void render() {

System.out.println("渲染Mac风格按钮");

}

}

// 具体产品：Windows输入框

public class WindowsInput implements Input {

@Override

public void render() {

System.out.println("渲染Windows风格输入框");

}

}

// 具体产品：Mac输入框

public class MacInput implements Input {

@Override

public void render() {

System.out.println("渲染Mac风格输入框");

}

}

1.  定义抽象工厂

// 抽象工厂：定义创建按钮和输入框的接口

public interface UIFactory {

Button createButton(); // 创建按钮

Input createInput(); // 创建输入框

}

1.  实现具体工厂

// 具体工厂：创建Windows系列组件

public class WindowsFactory implements UIFactory {

@Override

public Button createButton() {

return new WindowsButton(); // 返回Windows风格按钮

}

@Override

public Input createInput() {

return new WindowsInput(); // 返回Windows风格输入框

}

}

// 具体工厂：创建Mac系列组件

public class MacFactory implements UIFactory {

@Override

public Button createButton() {

return new MacButton(); // 返回Mac风格按钮

}

@Override

public Input createInput() {

return new MacInput(); // 返回Mac风格输入框

}

}

1.  客户端的使用

public class Client {

// 客户端只依赖抽象工厂和抽象产品，不涉及具体类

private static void renderUI(UIFactory factory) {

Button button = factory.createButton();

Input input = factory.createInput();

button.render();

input.render();

}

public static void main(String\[\] args) {

// 根据系统类型选择工厂（实际可通过配置文件动态获取）

String os = "Windows"; // 假设检测到系统是Windows

UIFactory factory;

if (os.equals("Windows")) {

factory = new WindowsFactory();

} else {

factory = new MacFactory();

}

// 渲染对应风格的UI，无需修改后续代码

renderUI(factory);

// 输出：

// 渲染Windows风格按钮

// 渲染Windows风格输入框

}

}

**总结：**抽象工厂模式能帮你创建 “**_风格统一、必须一起使用的一组产品_**”，并且在创建这些产品时，你不需要写死具体产品的类名，只需要通过抽象工厂就能拿到对应产品族的所有产品。（虽然该模式在日常开发中使用不高，但关键时候是能起大作用的。）

**实现Abstract Factory一些有用的技术(原文)：**

1.  **_将工厂设置为单例_**，具体工厂的核心作用是 “创建同一产品系列的产品”**，**而它自身通常是“无状态”的 —— 即工厂实例不保存任何可变数据，所有创建产品的逻辑都是固定的。
2.  **_创建产品，_**Abstract Factory仅声明一个创建产品的接口，真正创建产品是由具体工程子类实现的。最通常的办法是为每一个产品定义一个工厂方法(Factory Method)。如果由多个可能得产品系列，具体工厂也可以使用模版(Prototype)来实现。
    - 抽象工厂的接口中，每个“创建产品”的方法（如createButton()、createInput()）本质上就是一个**_工厂方法_**。具体工厂子类通过实现这些方法，完成具体产品的创建。
    - 当产品系列非常多（比如有 10 种 UI 风格，每种风格有 20 种组件），用工厂方法逐个实现createXXX()会导致代码冗余（每个具体工厂都要写 20 个方法）。这时可以用**_原型模式_**：让产品自身支持“克隆”，具体工厂通过“复制原型产品”来创建新实例，而非直接new。
3.  **_定义可扩展工厂，_**通过注册表等方式，让工厂能动态适配新的产品系列，避免硬编码，增加抽象工厂模式灵活性。

// 1. 工厂注册表：动态管理具体工厂

public class FactoryRegistry {

// 存储“产品系列名称→具体工厂”的映射

private static Map&lt;String, UIFactory&gt; factories = new HashMap<>();

// 注册具体工厂（如注册"Windows"对应WindowsFactory）

public static void registerFactory(String key, UIFactory factory) {

factories.put(key, factory);

}

// 根据名称获取工厂（客户端用这个方法）

public static UIFactory getFactory(String key) {

return factories.get(key);

}

}

// 2. 初始化时注册具体工厂（可在配置文件中配置）

public class AppInitializer {

static {

// 注册Windows工厂

FactoryRegistry.registerFactory("Windows", WindowsFactory.getInstance());

// 注册Mac工厂

FactoryRegistry.registerFactory("Mac", MacFactory.getInstance());

// 未来新增Linux工厂时，只需新增一行注册代码，无需修改其他逻辑

// FactoryRegistry.registerFactory("Linux", LinuxFactory.getInstance());

}

}

// 3. 客户端使用：通过名称获取工厂，无需硬编码

public class Client {

public static void main(String\[\] args) {

String os = "Windows"; // 可从配置文件动态获取

UIFactory factory = FactoryRegistry.getFactory(os); // 从注册表获取

factory.createButton().render();

}

}