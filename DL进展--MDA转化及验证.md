4.9

1. from www.omg.org: （1.5）
   1. about MDA:宗旨是为了应对快速变化的平台和环境，保留企业最重要的business functionality and behavior，这部分投资是企业最重要的也是比较稳定的。而OMG这个组织已经提出很多标准，比如UML（设计、分析软件系统）、XML（存储、传输结构化数据）、COMBA（允许不同语言构建的平台进行通信），而实现MDA这个标准恰恰用了之前已经提出过的标准。通过MAD设计标准，使得企业快速适应新技术、新环境。
   2. 启发：突然明白为什么指南上会要求使用MDA，这些工作是保留整个业务逻辑的工作
   3. 下一步： 通过简单案例了解整个开发过程，并了解其中的工具链怎么使用

4.10

1. 读《解析MDA》4-6章（2）：一个早餐订购系统如何使用MDA开发
   1. 开始构建的PIM非常简略
   2. 后面的PSM有关系模型的、有EJB模型的和web模型的，变换规则涉及了一些知识
   3. 生成代码更是涉及变换规则，应该有自动化工具，但是没看到书上提
   4. 问题：每个不同项目的转换需要自己去制定规则吗
5. 下一步：还是看1-3章，对UML等等建模语言先学习一下



4.14（3.5）

屈学弟目前在做的是CIM（用例）->PIM（序列）的转化和验证（毕设题目），现阶段已经做完转换，并且能生成PIM到MSVL的中间件AST了，下一步是提取性质验证。对于我这边的工作，可以做**元模型定义**，这是一种层次之间转换的方法，虽然MDA每个层用什么表示需要了解广泛一点再看转换，但是我发现可以直接看转换的输入输出来看表示。

## 元模型定义

### 《解析MDA》

1. 变换需要的特性：
   1. 可调
   2. 可追溯
   3. 增量一致：加了新的信息，重新生成的时候旧的信息生成和以前一样
   4. 双向性

2. MOF（Meta Object Facility)四层元模型：定义建模语言来建模
   1. M3：元元模型
   2. M2：元模型：UML的Class、Attribute等的定义
   3. M1：UML
   4. M0：现实世界实例

3. 变换规则定义：

   1. 像是一种编程语言那样的语法，还没理解如何和元模型去联系
   2. **4.15**不太懂，而且也仅仅看到了类的变换，还没有操作。 **变换定义语言**可关注。（2）

   

### GPT对于one-click full system的观点

目前不可能。对于一些structual code(classes, CRUD methods, data schemas数据模式)可以生成。

对于behavioral logic(complex algorithm, workflows, validations)目前还是需要手动编码。

想法：对于类、部分SQL代码，可以直接沿用现在的技术，把重点放在结合大模型上，因为只有大模型才能生成复杂逻辑。

GPT提到的工具：![image-20250415171555152](${imagines}/image-20250415171555152.png)



### 基于模型驱动框架的 Solidity代码生成研究  王梓 西南交通大学 硕士论文

#### 国内外研究现状

基于业务流程、UML、FSM、其他方法。

总结：缺乏由高阶抽象到具体实现的多层次的开发流程，转换跨度过大  ，自动化程度低。(1)

#### 技术路线

1. **4.16**重点关注PIM->PSM的技术内容：
   1. 把最终执行代码的语言特性整合到PIM元模型中
   2. 对比分析 PIM元模型和 PSM 元模型，设计模型间的转换规则  
   3. 使用ATl实现基于元模型的PIM->PSM转换
2. 重点关注PSM->Solidity代码的过程：
   1. 依据 PSM 元模型及转换需求编写了 Acceleo 的模板文件 ，输入转换工具后生成（需要人工微调）

#### 相关技术介绍

1. 2.1.2PIM到PSM转换介绍有相关论文（模板解析方法，还有直接从PIM的UML直接到代码的）
2. 2.1.3模型转换方法:元模型最主流，也是MDA规范方法（3）
3. 2.2有MDA相关规范，如XMI（统一文件格式）、OCL、QVT（模型转换规则标准）-- ATL（以QVT标准开发的语言）等等，后面看了具体的操作再回来看

#### 业务模型建模与转换（仅有伪代码）

1. 每层使用的UML图
   1. CIM -- 用例图 -- 系统功能需求
   2. ＰＩＭ -- 类图和状态机图（静态和动态） --  功能需求和行为
2. 用例图光有图不行，还要有用例描述。自然语言，使用固定句式
3. 类图：
   1. 类之间的关系含义：
      1. 关联：长期的结构关系，如互为成员变量
      2. 组合：整体和部分，但是部分不能独立存在，如人和人的手
      3. 聚合：整体和部分独立存在，如学校和学生
      4. 泛化：继承关系
      5. 实现：类实现接口的功能
4. 用例->类图（自动化后调整）：（考虑什么可以成为类，类之间的关系又如何？）
   1. 用户--类， 用例--类
   2. 关联关系--类关联；泛化关系--继承
   3. 再分析用例描述，动词分析->类的方法，名词分析->类名和属性等等。算法能做到大部分的类的构建，但是一些方法的参数、类之间的关系还需要人工补充。
5. 用例描述->状态图（没说最后需不需要调整）：
   1. 还是分析自然语言，并且有循环、条件的使用，还结合了智能合约的特点来设置trigger,guard,action等等（3）

#### 设计模型转换与代码生成  

怎么定义DSL？几种图的元模型怎么看？

**4.18**

1. 类图的元模型，在UML元模型的基础上，增加了适配solidity的构造型。比如：在 UML 的数据类型中并未直接提供映射的功能，因此需要增加map类型与 Solidity 中的mapping 类型构成对应关系  
2. 补充看元模型图知识：（这个表示和UML类图有些微区别在箭头上）（2）
   1. 箭头：（原文有歧义，实心空心都说是继承）（高亮部分）
      1. 空心箭头：继承，箭头指向父类
      2. 普通箭头：箭头指向被关联的类
   2. 菱形：
      1. 空心菱形：聚合：整体与部分，部分可独立存在，比如人和人的衣服的关系
      2. 实心菱形：组合：整体与部分，但是部分不能独立存在，比如人和人的头
   3. 数字关系：
      1. **`0..\*`**：零到无限多个（例如 `Parameter` 与 `Operation` 的关系，一个操作可以有多个参数）。
      2. **`1`**：必须有且仅有一个（如 `specific 1.1` 表示子类必须继承一个父类）。
      3. **`0..1`**：零或一个（如 `0..1 Nesting` 表示嵌套的类可有可无）。
      4. **`1..\*`**：至少一个
   4. Solidity的元模型设计没有说为什么，只是说了都有哪些。 直观感觉就是，元模型的设计就是要把目标语言的语言特性考虑好，有什么就加什么，**就是把目标语言的语言特性使用元模型表达出来**
   5. ATl转换规则基本结构：<img src="${imagines}/image-20250418155642603.png" alt="image-20250418155642603" style="zoom:70%;" />
      1. 例子不明不白，但是规则都是能看懂的
   6. PSM->Solidity：（2.5）
      1. 使用Acceleo工具，使用基于模板的方法


### 基于模型驱动架构的 Web 代码生成方法研究与应用（重庆邮电2018硕）

#### 引言
有很多可以参考的论文

#### 二、相关知识 -- 模型转换的方法
1. 模型示例转换
	1. 感觉像是拓展元模型
	
2. 应用设计模式的转换
	1. 暂时没看出来和1的区别，作者也说是1的拓展
	
3. 提到了MVC（model view control)，类似开发框架，有三种设计模式

   1. 策略设计模式：逻辑算法

   2. 组合设计模式：ui

   3. 观察者设计模式：数据变化则返回UI

      


#### 三、 Web代码生成方法
1. 流程： “标记领域模型得到对象模型”、 “转换对象模型到关系模型（这步有什么用呢？）”、 “转换对象模型到状态模型” 、“转换状态模型到表示层代码”
2. 两种模型表示方法：都是针对M3层来说的
   1. 继承：拓展M3
   2. 实例化：拓展M2，实例化M3 的元素
   3. 其实还有混合，得看具体的问题来选择
3. 加入表示层设计模式到元模型
   1. 论文感觉很绕，但本质其实都是在M2层的继承
   2. 这个拓展很简单，就是子类加了些字段，我需要思考这样做是不是必要的。
      1. 看完之后还是想不明白为什么必要，明明在M1层增加属性，在M1层进行继承也可以做到同样的效果
   3. 转换规则：类图到状态机图的
      1. 就是一堆映射，而且值得怀疑的是，空操作没有内容，状态机怎么个走向？走向是自己定义的：如展示页面--修改页面--

#### 四、 Web代码生成方法的应用

1. 生成对象模型：从UML类图，powerDesigner可以直接把类图转换成带java数据类型的类图，即所谓的对象模型，其实没改变什么
2. 生成关系模型：pD强项，直接把O/R映射关系生成了，还可以直接生成sql语句，应该在这个过程中手动设定了属性长度
3. 拓展状态图的元模型：page和一堆page继承了state。就像是换了个说法，我不叫状态了，而是每个状态就是每个不同的页面，确实也符合逻辑，就是有点牵强
4. 类图转换成状态图，并使用了之前的构造型，每个类一个状态图：ListPage、EditPage、CreatePage，也符合逻辑，比如查看学生信息、课程信息、试题信息
5. 创建表示层设计模式：就是 前端->controller->数据库，这应该是手动的。最后的表示层组件交互模型，应该也是手动
6. 最后生成表示层代码：从状态模型中获取属性，编写GTL模板使用PD进行生成（有HTML，有java)



### Model Transformation with ATL into MDA from CIM to PIM  Structured through MVC

摩洛哥一个大学，一个小小期刊： Procedia Computer Science
cite score:4.5（2025.5.6）

1. 总结：CIM使用了BPMN（bussiness process modeling notation)（也有一定的规范）,使用转换规则在BPMN model中提取了状态、用例、类图，并且把最后的类图按照MVC模式转换出来，叫structured class diagram

2. 转换方法：BPMN到ＰＩＭ

   1. 如论文中图所示，挺有道理的，可以直接参考，不列出来自己还真不会这么想

   2. 意义：ＢＰＭＮ图感觉是真正意义上的ＣＩＭ，全程关注业务不关注开发，但是需要人工理解需求建立BPMN图，这个是基础；转换后的PIM真正有了开发细节，比如类，以及MVC结构的类

   3. BPMN到状态图（比较直观，因为都是流程图）

      1. 数据对象--状态：数据对象需要被操作，因此数据对象有不同的状态就可以转换成状态
      2. 排他分支--条件（决策点）
      3. 排他汇聚（多个事件汇总，但是只有一个事件通过）--汇聚点（多个状态汇聚到一个）（可以通过结构变成排他汇聚）
      4. 平行分支--分支状态
      5. 平行汇聚--汇聚状态
      6. 序列流--状态转移

   4. BPMN到类：

      1. 数据对象--类
      2. 每个数据对象的状态--类的方法

   5. 类图到MVC模式的类图：

      1. 类内属性--MVC的model
      2. 类内方法--MVC的controller的操作
      3. 类内属性--view的text field(前端字段展示)
      4. 类内方法--view的action（前端页面改变或者数据改变）
      5. 类--model、view、controller

   6. 最后得到的图，应该是有的需要添加（2），有的需要删除的（3），无法面对所有的情况


### Model Transformation
Formal Methods for Model-Driven Engineering (SFM 2012) 暑期学校 springer出版论文集
#### introduction

 change propagation 和 metamodel/model coupled evolution用来**评估**模型转换的潜力和意义
change propagation（转换的时候源和目标都要更新）解决方法：

1. 用双向转换语言
2. 手动前向和后向编程转换并人为确保一致性

metamodel/model coupled evolution（随着时间推移模型和元模型需要一起更新）

#### classification
几种分类还不是很理解，看完下面的语言回过头再看看

<<<<<<< HEAD
**操作式（Operational / Imperative）**

- **特点**：在直接操纵基础上，专门在元建模语言（如 EMOF）或查询语言（如 OCL）中加入命令式扩展。仍需程序员指定执行顺序，但语法更贴近模型开发。
- **示例**：QVT Operational Mappings、XMF、MTL、Kermeta 等把 OCL 加上赋值、循环等结构，用于实现更复杂的逻辑【turn0file1†L16-L20】；或者 QVT 的 Black-box 调用外部 Java 代码。

**关系式（Relational / Declarative）**

- **特点**：以数学“关系”为核心，通过规则（constraints or relations）描述源模型元素类型与目标元素类型之间的映射，强调声明“是什么”而非“怎么做”。通常是**无副作用**，在规则中隐式创造目标元素，可自然支持双向（bidirectional）和回溯（backtracking）。
- **示例**：QVT Relations、基于 Triple Graph Grammars (TGGs) 的实现（如 MoFLON）、以及将逻辑／答案集编程（ASP）用于转换的 JTL【turn0file0†L1-L8】。

**混合式（Hybrid）**

- **特点**：组合声明式和命令式，既允许用 declarative 规则快速表达简单映射，也可在必要处嵌入 imperative 代码以完成复杂处理。
- **示例**：ATL（既有 declarative matched rules，也有 imperative action blocks）、ETL（Epsilon Transformation Language）【turn0file0†L8-L11】。

**图变换（Graph-Transformation Based）**

- **特点**：把模型视作有属性的图（节点+边），以图重写规则（LHS→RHS）来表达转换。规则可就地（in-place）修改模型，调度可隐式（FIX点）或显式（状态机、数据流）管理。
- **示例**：AGG、AToM3（隐式 fix-point）、VIATRA2（基于 ASM 的显式控制）、GReAT（数据流图调度）【turn0file0†L12-L20】。

**规则式（Rule-Based）**

- **特点**：以“guard ⇒ action”为基本单元，运行时根据 guard 条件触发对应 action，触发冲突（多个 guard 同时为真）时由运行时解决。强调规则的封装、自包含，便于组合和重用。
- **示例**：一些 DSL 或工作流引擎中，直接写 guard/action 规则；也有研究将这种风格用于模型转换【turn0file2†L17-L24】。
=======
#### Language
看完感觉也是白看，除了ATL有格式例子，都是在说语言特性（是什么方法类型的、单向双向、声明式还是命令式等等）
- **操作式（Operational / Imperative）**
    
    - **特点**：在直接操纵基础上，专门在元建模语言（如 EMOF）或查询语言（如 OCL）中加入命令式扩展。仍需程序员指定执行顺序，但语法更贴近模型开发。
        
    - **示例**：QVT Operational Mappings、XMF、MTL、Kermeta 等把 OCL 加上**赋值、循环**等结构，用于实现更复杂的逻辑或者 QVT 的 Black-box **调用外部 Java 代码**。
```
    - 示例代码：QVT Operational Mappings
    -- 指定：输入模型 p，输出模型 eModel
transformation Person2Employee(in  pModel : SourceMM,
                                out eModel : TargetMM);

-- 一个 mapping：将源模型 pModel 中所有 Person 转为 Employee
mapping Person2EmployeeMapping(srcRoot : SourceMM!Root) : TargetMM!Root {
  do {
    -- 假设 TargetMM 有个 Root 容器来收所有 Employee
    eModel := TargetMM!Factory.createRoot();

    -- 显式遍历源模型中的所有 Person
    for (p : SourceMM!Person in srcRoot.persons) {
      -- 手动创建目标对象
      emp : TargetMM!Employee := TargetMM!Factory.createEmployee();

      -- 显式赋值
      emp.employeeName := p.name;
      emp.seniority    := p.age div 10;

      -- 插入到输出模型的容器里
      eModel.employees.add(emp);
    }
  }
}
```

        
- **关系式（Relational / Declarative 声明式）**
    
    - **特点**：以数学“关系”为核心，通过规则（constraints or relations）描述源模型元素类型与目标元素类型之间的映射，强调声明“是什么”而非“怎么做”。通常是**无副作用**，在规则中隐式创造目标元素，可自然支持双向（bidirectional）和回溯（backtracking）。
        
    - **示例**：QVT Relations、基于 Triple Graph Grammars (TGGs) 的实现（如 MoFLON）、以及将逻辑／答案集编程（ASP）用于转换的 JTL【turn0file0†L1-L8】。
```
    - 示例代码：QVT Relations
-- 转换声明：指定源模型和目标模型
transformation Person2Employee
    source src : SourceMM,
    target trg : TargetMM;

-- 最顶层关系，声明 Person 和 Employee 的对应
top relation Person2Employee {
    -- 源模型中要匹配的元素
    domain src  p : SourceMM!Person;
    -- 目标模型中要创建或匹配的元素
    domain trg  e : TargetMM!Employee;

    where {
        -- 在“where”中只写约束：Employee 的属性如何等于 Person 的属性
        e.employeeName = p.name;
        e.seniority    = p.age  / 10;  -- QVT OCL 中用 / 表示除法
    }
}

```

        
- **混合式（Hybrid）**
    
    - **特点**：组合声明式和命令式，既允许用 declarative 规则快速表达简单映射，也可在必要处嵌入 imperative 代码以完成复杂处理。
        
    - **示例**：ATL（既有 declarative matched rules，也有 imperative action blocks）、ETL（Epsilon Transformation Language）【turn0file0†L8-L11】。
        
- **图变换（Graph-Transformation Based）**
    
    - **特点**：把模型视作有属性的图（节点+边），以图重写规则（LHS→RHS）来表达转换。规则可就地（in-place）修改模型，调度可隐式（FIX点）或显式（状态机、数据流）管理。
        
    - **示例**：AGG、AToM3（隐式 fix-point）、VIATRA2（基于 ASM 的显式控制）、GReAT（数据流图调度）【turn0file0†L12-L20】。
    
    -示例代码：
```
    TypeGraph PersonEmployeeTG {
  // 两种节点类型
  NodeType Person {
    attributes
      name : String;
      age  : Integer;
  }
  NodeType Employee {
    attributes
      employeeName : String;
      seniority    : Integer;
  }
  // 没有边类型（本例中无需建模引用）
}

HostGraph Initial {
  conformsTo PersonEmployeeTG;

  // 举例：模型里有两个 Person 实例
  nodes
    p1 : Person { name = "Alice"; age = 34; }
    p2 : Person { name = "Bob";   age = 27; }
}

Rule Person2Employee {
  // 1) LHS（左侧）——要匹配的子图
  LHS {
    p : Person;           // 匹配任意一个 Person 节点
  }
  // 2) RHS（右侧）——匹配后，图应变成什么
  RHS {
    p : Person;           // 原 Person 节点保留
    e : Employee;         // 新创建一个 Employee 节点
  }
  // 3) Attribute Conditions（属性赋值）
  AttributeAssignments {
    e.employeeName = p.name;       // 复制 name
    e.seniority    = p.age  / 10;  // age 除以 10 得到 seniority
  }
  // 4) 没有 Negative Application Conditions（本例不需阻止情形）
}

```
	- 和声明式的区别：
		- 声明式非就地：重新创建一个模型
		- 图变换有更细的调度控制：可以指定规则的应用顺序、并行/串行、递归触发、甚至把调度也当作图模式来编写
		- 图变换背后有一整套代数图变换（Algebraic Graph Transformation）理论，包括同余、合成公理、双向箭射（Pushout/Pushout Complement）等；声明式更多基于逻辑约束或模型管理的工程实践，缺少同样级别的“数学公理”支持。
- **规则式（Rule-Based）**
    
    - **特点**：以“guard ⇒ action”为基本单元，运行时根据 guard 条件触发对应 action，触发冲突（多个 guard 同时为真）时由运行时解决。强调规则的封装、自包含，便于组合和重用。
        
    - **示例**：一些 DSL 或工作流引擎中，直接写 guard/action 规则；也有研究将这种风格用于模型转换【turn0file2†L17-L24】。
```java
    // 示例代码
    // 1. 定义事实类型（POJO）
public class Person {
    private String name;
    private int age;
    // 构造、getter/setter 略
}

public class Employee {
    private String employeeName;
    private int seniority;
    // 构造、getter/setter 略
}

// 2. Drools 规则文件 Person2Employee.drl
package com.example.rules;

import com.example.model.Person;
import com.example.model.Employee;

rule "Person to Employee"
when
    // Guard：匹配任何一个 Person 事实
    $p : Person( $n : name, $a : age )
then
    // Action：创建一个新的 Employee 并插入 WM
    Employee e = new Employee();
    e.setEmployeeName($n);
    e.setSeniority($a / 10);
    insert( e );
    System.out.println("Created Employee for Person: " + $n);
end
``` 
- 与命令式的对比：以guard ⇒ action 为最小单位，依赖引擎的匹配机制，**无需手写循环**，但可以在 action 里写任意 Java 逻辑

## 案例1：Janus transformation language（关系式/约束式语言）
解决问题:在模型驱动工程中，往往需要对同一对**源模型**与**目标模型**进行双向修改并保持一致，但传统的单向转换或手工维护前后转换并不能保证一致性，也难以应对“非双射”和“非完备”的变换。
概念解释：
	- 模型的双射：源模型和目标模型一一对应。不会出现按照对应规则映射后有多个结果的情况
	 - 非完备：对目标模型进行修改后按照现有规则无法再映射回源模型

### JTL特点
- **非双射支持（Non-bijectivity）**
    
    - 在展平操作中，不同的分层模型可能映射到相同的平坦模型，反向传播时会产生**多个**可能的源模型。JTL 能一次性**并行**生成所有候选源模型，供设计者选择或后续自动策略进一步决定。
        
- **模型近似（Model Approximation）**
    
    - 当手工改动使得平坦模型不再是任何正向转换的直接产物（即非完备），JTL 利用**追踪信息（trace links）**，通过搜索“最接近”的源模型来**近似**恢复一致性；同时保留对那些无法映射回源模型的属性改动作为注释，以免丢失额外信息。
        
- **统一双向语义**
    
    - 一个 JTL 程序即同时描述了正向与逆向转换，且“源/目标”仅由执行方向决定——无需维护两套手写的单向脚本，从语言层面保证了转换的一致性和可逆性。
- ![[Pasted image 20250517191509.png]]
## JTL 执行架构（见图 8）

- **输入**：
    
    1. **源/目标元模型**（EMF Ecore 格式）
        
    2. **源模型**（如 HSM）
        
    3. **JTL 转换脚本**（QVT-like 语法）
        
- **流程**：
    
    1. **序列化为 ASP 断言**：将元模型和源模型编码为 DLV 可处理的谓词（`metanode/2`、`node/3`、`edge/5` 等）。
        
    2. **语义锚定**：通过 ATL 自动将 JTL 脚本翻译成 ASP 规则和约束（参见 Listing 1.3），包括关系声明（`relation("r2", HSM, state)`）及对应的约束语句。
        
    3. **ASP 求解**：DLV 求解器执行所有命题规则，**非确定性地**生成所有可能的靶模型或候选源模型（stable models）。
        
- **输出**：若正向执行，可得平坦 NHSM；若逆向执行，可得一组 HSM 候选模型，或最佳近似模型。

### JTL 如何保证前后一致性

1. **追踪信息（Trace Links）**
    
    - 在正向转换时，JTL 会把每个生成的目标元素与对应的源元素关联起来，形成“追踪节点（trace_node）”和“追踪边（trace_edge）” 。
        
    - 当逆向传播修改时，如果目标元素仍在追踪信息中，JTL 能**一一映射**回原来的源元素，从而保证对已有元素的重命名或属性变更能精确同步。
        
2. **模型近似（Model Approximation）**
    
    - 对于那些不在追踪信息中、或目标侧新增却在源模型中无对应的元素（如Δ3中的额外注释），JTL 不会盲目丢弃。它会把这些“超出映射范围”的改动保留为**附加信息**，并在生成的“近似”源模型中以注释或扩展属性形式存储，避免数据丢失 。
        
3. **自动生成多候选源模型**
    
    - 面对非双射情形（如Δ2新增的转换既可能映射到父复合状态，也可能映射到子状态），JTL 能一次性**并行**地生成所有合理的候选源模型集 $M^*$，由用户或后续策略挑选最优解 。
        
4. **Reachability 与 Choice Preservation**
    
    - **Reachability**：任何由 JTL 逆向生成的候选源模型，再经正向转换，都必须能“回到”原始的目标模型版本；
        
    - **Choice Preservation**：一旦用户在候选集中选定了某个源模型，JTL 保证对其进行正向再转换，能恢复出与目标模型**完全一致**的结果
>>>>>>> 3f41f57f5f70b1bf7cf6fd8f8bf889fada92cdf6
