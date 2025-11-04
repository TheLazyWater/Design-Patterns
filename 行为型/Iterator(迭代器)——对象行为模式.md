**Interator**

**类型**：命令型

**定义**：提供一种方法顺序访问一个容器对象中的各个元素，而又不需要暴露该对象的内部表示。

迭代器模式如此常用，以至于很多编程语言都默认实现了此模式，所以虽然我们天天都在用迭代器模式，但却很少有自己实现的机会。

其核心动机是为了**在迭代一个容器对象的同时不暴露其内部细节**，这啥意思呢？

例如ArrayList类，当我们需要迭代它的元素的时候，我们不可能将其内部存储元素用的数据结构，例如数组，暴露给用户。那样的话不仅将来更换数据结构变的不可能，而且如果将内部细节暴露给了客户端，那么就无法控制客户端对其行为了。

**使用场景**

在java开发中，当你要你要实现自己的容器类，且要迭代里面的元素时。换句话还可以说，**当你要使自己的类支持foreach 操作的时候使用此模式**。(**这种话在C#同样适用**)

在Java中没有必要自己实现这个模式，因为Java类库默认支持了此模式，所以此处我们也以Java官方类库为例谈论一下，因为我想写一些有用的东西，而不是华而不实的东西。

**实例**

C# 对迭代器模式有**原生支持**，通过两个核心接口定义了迭代器的规范

|     |     |     |
| --- | --- | --- |
| **接口** | **角色** | **作用** |
| IEnumerable | 抽象聚合接口 | 表示 “可被迭代的聚合对象”，定义了 GetEnumerator() 方法，用于获取迭代器。 |
| IEnumerator | 抽象迭代器接口 | 表示 “迭代器”，负责实际遍历元素：<br><br>\- Current：获取当前元素；<br><br>\- MoveNext()：移动到下一个元素（返回 bool 表示是否有下一个）；<br><br>\- Reset()：重置迭代器到初始位置。 |

我们通过一个 “书架（BookShelf）” 例子，手动实现迭代器，理解其工作原理：

第一步：定义元素类

// 元素：书籍

public class Book {

public string Name { get; }

public Book(string name) {

Name = name;

}

}

第二步**：**实现具体聚合（书架）

书架是 “可被迭代的聚合对象”，需实现 IEnumerable 接口，返回自定义迭代器**：**

// 具体聚合：书架（可存放书籍，支持迭代）

public class BookShelf : IEnumerable {

private List&lt;Book&gt; \_books = new List&lt;Book&gt;(); // 内部存储结构（不暴露给外部）

// 添加书籍

public void AddBook(Book book) {

\_books.Add(book);

}

// 实现IEnumerable：返回迭代器

public IEnumerator GetEnumerator() {

// 返回自定义的书架迭代器（持有当前书架的引用）

return new BookShelfIterator(this);

}

// 提供内部方法：让迭代器能访问书架的元素（不直接暴露List）

internal Book GetBookAt(int index) {

return \_books\[index\];

}

internal int Count => \_books.Count; // 书籍数量

}

第三步：实现具体迭代器

迭代器负责遍历书架中的书籍，需实现 IEnumerator 接口：

// 具体迭代器：书架迭代器（负责遍历BookShelf中的书籍）

public class BookShelfIterator : IEnumerator {

private BookShelf \_bookShelf; // 持有聚合对象的引用

private int \_currentIndex = -1; // 当前遍历位置（初始为-1，未开始遍历）

public BookShelfIterator(BookShelf bookShelf) {

\_bookShelf = bookShelf;

}

// 获取当前元素（需转换为Book类型）

public object Current => \_bookShelf.GetBookAt(\_currentIndex);

// 移动到下一个元素：索引+1，返回是否有效

public bool MoveNext() {

\_currentIndex++;

return \_currentIndex < \_bookShelf.Count;

}

// 重置迭代器（回到初始位置）

public void Reset() {

\_currentIndex = -1;

}

}

第四步：客户端的使用

class Program {

static void Main(string\[\] args) {

// 创建书架（具体聚合）

BookShelf shelf = new BookShelf();

shelf.AddBook(new Book("《设计模式》"));

shelf.AddBook(new Book("《C#入门》"));

shelf.AddBook(new Book("《迭代器模式详解》"));

// 用foreach遍历（自动使用迭代器）

Console.WriteLine("书架中的书籍：");

foreach (Book book in shelf) { // 等价于：var enumerator = shelf.GetEnumerator(); while(enumerator.MoveNext()) { ... }

Console.WriteLine(book.Name);

}

}

}

**总结**

没什么好总结，就是这么简单。