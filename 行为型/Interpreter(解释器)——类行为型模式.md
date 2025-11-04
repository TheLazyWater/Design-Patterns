**Interpreter**

**类型**：行为型

**定义**：定义一种语言的语法规则，并构建一个解释器来解释这种语言中的句子（表达式）

它通过将复杂的语法分解为简单的 “原子表达式” 和 “组合表达式”，逐步解析并执行，最终得到结果。

**使用场景**

生活中，我们经常需要处理各种 “自定义规则” 或 “表达式”：

- 数学表达式（如 1 + 2 \* 3）；
- 正则表达式（如 \\d{3}-\\d{4} 匹配电话号码）；
- 简单的业务规则（如 “消费满 1000 且会员等级≥3 可享受 8 折”）

这些“表达式”本质上是一种“迷你语言”，有自己的语法规则。如果直接用代码硬编码解析（比如用大量 if-else 处理 +、\* 运算），会导致代码臃肿、难以维护（比如新增 % 运算时，需要修改大量代码）。

**参与者**

1.  **Abstract Experssion(抽象表达式)**: 定义所有表达式的**共同接口**，声明一个 Interpret() 方法（解释方法），用于解析表达式并返回结果。
2.  **Terminal Expression(终结符表达式)**：表示语法中的**最小单元（不可再分）**，如数学表达式中的 “数字”（1、2）、正则表达式中的 “字符”（a、b）。它实现 Interpret() 方法，直接返回自身的值。
3.  **Non-terminal Expression(非终结符表达式)**: 表示语法中的**组合规则**（由多个表达式组合而成），如数学表达式中的 “运算符”（+、-、\*）。它包含对其他表达式（终结符或非终结符）的引用，Interpret() 方法会递归调用子表达式的解释方法，再结合自身规则计算结果。
4.  **Context(上下文)**：存储解析过程中需要的**全局信息**（如表达式字符串、中间结果），供所有表达式共享。

**实例(C#)**

以解析 “加法和减法表达式”（如 1 + 2 - 3）为例，用解释器模式实现：

第一步：定义抽象表达式

// 抽象表达式：所有表达式的共同接口

public abstract class Expression {

// 解释方法：返回表达式的计算结果

public abstract int Interpret();

}

第二步：**实现终结符表达式（Terminal Expression）**

终结符是 “数字”，直接返回自身的值：

// 终结符表达式：表示数字（不可再分的最小单元）

public class NumberExpression : Expression {

private int \_number; // 存储数字值

public NumberExpression(int number) {

\_number = number;

}

// 解释：直接返回数字值

public override int Interpret() {

return \_number;

}

}

第三步：**实现非终结符表达式（Non-terminal Expression）**

// 非终结符表达式：加法（由两个子表达式组成）

public class AddExpression : Expression {

private Expression \_left; // 左操作数（可以是数字或其他表达式）

private Expression \_right; // 右操作数

public AddExpression(Expression left, Expression right) {

\_left = left;

\_right = right;

}

// 解释：先计算左、右表达式的结果，再相加

public override int Interpret() {

return \_left.Interpret() + \_right.Interpret();

}

}

// 非终结符表达式：减法

public class SubtractExpression : Expression {

private Expression \_left;

private Expression \_right;

public SubtractExpression(Expression left, Expression right) {

\_left = left;

\_right = right;

}

// 解释：左表达式结果 - 右表达式结果

public override int Interpret() {

return \_left.Interpret() - \_right.Interpret();

}

}

第四步：**构建上下文和客户端解析逻辑**

// 上下文：存储表达式字符串和解析过程中的临时信息

public class Context {

public string Expression { get; } // 原始表达式字符串

private int \_index; // 当前解析位置（用于遍历字符串）

public Context(string expression) {

Expression = expression;

\_index = 0;

}

// 读取下一个字符（跳过空格）

public char NextChar() {

while (\_index < Expression.Length && char.IsWhiteSpace(Expression\[\_index\])) {

\_index++; // 跳过空格

}

if (\_index >= Expression.Length) return '\\0'; // 结束符

return Expression\[\_index++\];

}

// 回退一个字符（解析错误时调整位置）

public void Back() {

\_index--;

}

// 读取下一个数字（如"123"）

public int NextNumber() {

int number = 0;

char c = NextChar();

while (char.IsDigit(c)) {

number = number \* 10 + (c - '0');

c = NextChar();

}

Back(); // 刚才多读了非数字字符，回退

return number;

}

}

// 客户端：构建表达式树并解析

class Program {

static void Main(string\[\] args) {

// 要解析的表达式："1 + 2 - 3"

string input = "1 + 2 - 3";

Context context = new Context(input);

// 手动构建表达式树（实际中可通过递归解析自动构建）

// 步骤：1 + 2 → 结果再 - 3

Expression left = new NumberExpression(context.NextNumber()); // 1

char op = context.NextChar(); // '+'

Expression right = new NumberExpression(context.NextNumber()); // 2

Expression addExpr = new AddExpression(left, right); // 1 + 2

op = context.NextChar(); // '-'

right = new NumberExpression(context.NextNumber()); // 3

Expression subtractExpr = new SubtractExpression(addExpr, right); // (1+2) - 3

// 解释执行表达式树

int result = subtractExpr.Interpret();

Console.WriteLine($"{input} = {result}"); // 输出：1 + 2 - 3 = 0

}

}

**使用场景**

1.  **语法规则简单的 “迷你语言”**：如简单的数学表达式、配置文件解析、业务规则引擎（如 “满减”“折扣” 规则）。
2.  **需要频繁解析同一类表达式**：如编译器对代码的语法解析、正则表达式引擎对模式的匹配。
3.  **语法可通过类结构表示**：语法规则能分解为 “终结符” 和 “非终结符”，且每个规则对应一个类。

**总结：**

使用场景可能很少，了解即可。