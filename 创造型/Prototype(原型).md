**Prototype**

**类型：**创造性模式

**定义：**用**_原型实例_**指定**_创建对象的种类_**，并且通过**_复制这些原型创建新的对象_**。

原型模式允许你通过复制已有的对象来创建新对象，而不需要重新创建对象的每个细节。

**举例：**

你是一名画家，现在你创造了这个实际最伟大的作品。几十年后人们发明了复制机器，现在你的作品可以被无限量的复制出来，桀桀桀。

**参与角色**

|     |     |
| --- | --- |
| Prototype | 声明一个克隆自身的接口 |
| ConcretePrototype | 实现一个克隆自身的操作 |
| Client | 让一个原型克隆自身从而创建一个新的对象。 |

**案例(这次用C#)：**

假设我们需要创建 “员工” 对象，每个员工包含基础信息（ID、姓名）和复杂信息（所属部门、技能列表）。员工对象的初始化需要模拟 “从数据库加载数据” 的耗时操作，因此适合用原型模式通过克隆快速创建新对象。

原型设计模式的克隆行为主要包含两种，**浅克隆**和**深克隆**。

第一步：定义原型类及相关引用类型

首先定义员工类（Employee），包含值类型属性（Id、Name）和引用类型属性（Department 部门类、Skills 技能列表）：

using System;

using System.Collections.Generic;

using System.IO;

using System.Runtime.Serialization.Formatters.Binary;

// 引用类型：部门（包含部门名称和编号）

public class Department

{

public int DeptId { get; set; }

public string DeptName { get; set; }

public Department(int deptId, string deptName)

{

DeptId = deptId;

DeptName = deptName;

}

}

// 原型类：员工（实现ICloneable接口）

public class Employee : ICloneable

{

// 值类型属性

public int Id { get; set; }

public string Name { get; set; }

// 引用类型属性

public Department Dept { get; set; } // 部门（自定义引用类型）

public List&lt;string&gt; Skills { get; set; } // 技能列表（引用类型）

// 构造函数：模拟耗时初始化（如数据库查询）

public Employee(int id, string name, Department dept)

{

Id = id;

Name = name;

Dept = dept;

Skills = new List&lt;string&gt;();

// 模拟耗时操作：从数据库加载技能

Console.WriteLine("初始化员工（耗时操作）...");

Skills.Add("C#编程");

Skills.Add("数据库设计");

}

// 【浅克隆】实现ICloneable接口（仅复制值类型和引用地址）

public object Clone()

{

// 调用MemberwiseClone()完成浅克隆：值类型复制，引用类型复制地址

return this.MemberwiseClone();

}

// 【深克隆】自定义方法（递归复制所有引用类型）

public Employee DeepClone()

{

// 1. 先浅克隆对象本身

Employee clone = (Employee)this.MemberwiseClone();

// 2. 手动复制引用类型成员（避免共享引用）

clone.Dept = new Department(this.Dept.DeptId, this.Dept.DeptName); // 复制部门对象

clone.Skills = new List&lt;string&gt;(this.Skills); // 复制技能列表（元素也会被复制）

return clone;

}

// 辅助方法：打印员工信息（用于验证克隆结果）

public void PrintInfo(string label)

{

Console.WriteLine($"\\n--- {label} ---");

Console.WriteLine($"ID: {Id}, 姓名: {Name}");

Console.WriteLine($"部门: {Dept.DeptId}-{Dept.DeptName}");

Console.WriteLine($"技能: {string.Join(", ", Skills)}");

}

}

第二步：客户端使用原型

class Program

{

static void Main(string\[\] args)

{

// 1. 创建原型对象（初始化耗时）

Department dept = new Department(1, "研发部");

Employee prototype = new Employee(1001, "张三", dept);

prototype.PrintInfo("原型员工");

// 2. 浅克隆：测试引用类型是否共享

Employee shallowClone = (Employee)prototype.Clone();

shallowClone.Id = 1002; // 修改值类型（不影响原型）

shallowClone.Name = "李四"; // 修改string（string是不可变类型，实际是新对象）

shallowClone.Dept.DeptName = "技术部"; // 修改引用类型的属性（会影响原型）

shallowClone.Skills.Add("项目管理"); // 修改引用类型的集合（会影响原型）

shallowClone.PrintInfo("浅克隆员工");

prototype.PrintInfo("浅克隆后，原型员工的变化（引用类型被修改）");

// 3. 深克隆：测试引用类型是否独立

Employee deepClone = prototype.DeepClone();

deepClone.Id = 1003;

deepClone.Name = "王五";

deepClone.Dept.DeptName = "产品部"; // 修改引用类型的属性（不影响原型）

deepClone.Skills.Add("需求分析"); // 修改引用类型的集合（不影响原型）

deepClone.PrintInfo("深克隆员工");

prototype.PrintInfo("深克隆后，原型员工的变化（引用类型不受影响）");

}

}

**浅克隆（Shallow Clone）**

通过 MemberwiseClone() 实现，**值类型会被复制，引用类型（如 Department、List&lt;string&gt;）仅复制引用地址（新对象和原型共享同一个引用对象）**。

**缺点**：修改克隆对象的引用类型属性时，会影响原型对象（如案例中浅克隆后，原型的部门名称和技能列表被修改）。

**深克隆（Deep Clone）**

在浅克隆基础上，手动复制所有引用类型成员（如案例中 DeepClone() 方法重新创建 Department 对象和 List&lt;string&gt; 集合）。

**优点**：克隆对象与原型对象的引用类型完全独立，修改互不影响，适合复杂对象。

**总结：**

快速复制对象实例，比重新创建对象而去繁琐地去初始化对象属性要好的多。就是深度拷贝的算法编写过程比较繁琐。这种设计模式已经被归纳为C#的语法基础了…