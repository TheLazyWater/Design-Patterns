**Composite组合——对象结构模式**

**类型**：结构型

**定义**：组合模式允许以相同的方式处理单个对象和对象的组合体

**使用场景**：

1\. 当你的程序结构有**类似树一样的层级关系**时，例如文件系统，视图树，公司组织架构等等

2\. 当你要以统一的方式操作单个对象和由这些对象组成的组合对象的时候。

**UML类图**

**参与者：**

|     |     |
| --- | --- |
| Component | 抽象类，定义统一的处理操作 |
| Leaf | 叶子节点，即单个对象 |
| Composite | 组合对象，这里使用透明方式，持有一个List&lt;Component&gt;组件 |
| Client | 通过Component接口操控组合部件的对象。 |

我们使用了组合模式中所谓的**透明方式**，因为我们将单个对象和组合对象按照完全一样的事物对待了，所以接口对外很透明。统一操作都是在Component中定义的，所有继承至它的节点都要实现，而有些操作叶子节点是不支持的，例如添加移除节点等，这样就要求叶子节点处理好这些方法。

**实例**

**第一步：设计一个个体与组合通用的接口**

定义对外展示的统一处理接口

public abstract class OrganizationComponent {

private String name;

public OrganizationComponent(String name) {

this.name = name;

}

public String getName() {

return name;

}

public abstract void add(OrganizationComponent organization);

public abstract OrganizationComponent getChild(String orgName);

public abstract int getStaffCount();

@Override

public String toString() {

return name;

}

}

**第二，组合类**

此类持有一个List&lt;OrganizationComponent&gt;，并继承OrganizationComponent。

public class OrganizationComposite extends OrganizationComponent {

//很关键，这体现了组合的思想

private List&lt;OrganizationComponent&gt; organizations = new ArrayList<>();

public OrganizationComposite(String name) {

super(name);

}

@Override

public void add(OrganizationComponent organization) {

organizations.add(organization);

}

@Override

public OrganizationComponent getChild(String orgName) {

for (OrganizationComponent org : organizations) {

OrganizationComponent targetOrg = org.getChild(orgName);

if (targetOrg != null) {

return targetOrg;

}

}

return null;

}

@Override

public int getStaffCount() {

int count = 0;

for (OrganizationComponent organization : organizations) {

count += organization.getStaffCount();

}

return count;

}

}

**第三，叶子节点**

叶子节点就是单个对象了，我们要使用合适的方式处理那些叶子节点不支持的对外接口方法。因为用户使的时候只会看到对外暴露的统一接口，他不知道此对象是叶子节点还是组合对象。

public class ItDepartment extends OrganizationComponent {

public ItDepartment(String name) {

super(name);

}

@Override

public int getStaffCount() {

return 20;

}

@Override

public void add(OrganizationComponent organization) {

throw new UnsupportedOperationException(this.getName()+"已经是最基本部门，无法增加下属部门");

}

@Override

public OrganizationComponent getChild(String orgName) {

if(getName().equals(orgName)){

return this;

}

return null;

}

}

// 其他叶子节点类似

...

**第四，客户端**

我们来看看是否达到了我们的设计目的：以统一的接口操作单个对象与其组合对象。

首先构建一个组合对象。模拟构建一家公司，公司下设行政部门和IT部门，还有一个天津分公司，而天津分公司又下设一个行政部门和IT部门。

然后我确定查询这个公司任何部门的员工人数。我们可以看到在查询过程中，我们没有判断当前对象到底是什么部门对象，都是以统一的接口在操作。

public class CompositeClient {

private OrganizationComponent constructOrganization() {

//构建总部

OrganizationComposite head = new OrganizationComposite("总公司");

AdminDepartment headAdmin = new AdminDepartment("总公司行政部");

ItDepartment headIt = new ItDepartment("总公司It部");

head.add(headAdmin);

head.add(headIt);

//构建分公司

OrganizationComposite branch1 = new OrganizationComposite("天津分公司");

AdminDepartment branch1Admin = new AdminDepartment("天津分公司行政部");

ItDepartment branch1It = new ItDepartment("天津分公司It部");

branch1.add(branch1Admin);

branch1.add(branch1It);

//将分公司加入到head中

head.add(branch1);

return head;

}

public void listOrgInfo() {

OrganizationComponent org = constructOrganization();

System.out.println(String.format("%s共有%d名员工",

org.getName(), org.getStaffCount()));

OrganizationComponent subOrg = org.getChild("天津分公司行政部");

System.out.println(String.format("%s共有%d名员工",

subOrg.getName(), subOrg.getStaffCount()));

}

}

**输出**

总公司共有140名员工

天津分公司行政部共有50名员工

**技术要点总结**

组合模式有所谓的**透明方式**与**安全方式**，是对于使用者而言的

**透明方式将所有对外操作都放在Component，叶子节点也得提供这些接口，虽然它实际上不支持这些操作**。而**安全方式只将叶子节点与组合对象同时提供的操作放在Component**。

为啥叫**透明方式**呢？因为用户使用的时候根本不管是叶子节点，还是组合对象，反正看到的接口都一样。这样就不安全了，因为万一这个对象是个叶子节点，假设你又使用了一个它不能提供的操作，例如add，就出问题了...

1.  通用操作定义在Component中，根据使用方式不同，透明方式与安全方式，有一定的不同
2.  组合节点Composite不仅要继承Component，而且要持有一个Component的集合
3.  叶子对象只继承Component即可

**优缺点**

**优点**

1.  可以递归组合成任意复杂的对象
2.  可随意增加新类型的Composite与Leaf的类
3.  简化了客户端代码，因为不论对象多么复杂客户端都是以同一套接口操作

**缺点**

1.  如果需要确定某个组件是特殊组织，然后针对它做特殊的操作，就需要在运行时判断。
2.  类多了

**总结**

当你需要处理“树状层级”且希望“统一操作叶子和节点”时，组合模式是最佳选择。