# CMU 15-445/645: Relational Model & Algebra

> **课程简介**：本章主要介绍数据库的抽象层次、关系模型的核心概念以及关系代数的基本运算。

## 1. Database Abstractions (数据库抽象)

在深入关系模型之前，我们需要定义数据抽象的三个基本层次：

*   **Data Model (数据模型)**
    *   **定义**: Abstraction for describing the data. (用来描述数据的抽象概念)
    *   **特点**: 关注高层的实体 (Entities) 和属性 (Attributes)。
*   **Relational Model (关系模型)**
    *   **定义**: How data is structured logically. (数据在逻辑上是如何构成的)
    *   **核心**: 基于 **Relations (关系)** 定义抽象，避免了底层的维护开销。
*   **Schema (模式)**
    *   **定义**: Description of a specific collection of data using a given data model. (特定数据库的结构描述)
    *   **类比**: 它是数据库的“蓝图” (Blueprint)。

---

## 2. The Relational Model (关系模型)

这是现代 DBMS 中最主流的数据模型。

### Core Tenets (核心原则)
1.  **Structure (结构)**: 数据存储在被称为 **Relations** (即表 Table) 的简单数据结构中。
2.  **Integrity (完整性)**: 确保存储的数据满足特定的约束条件 (Constraints)。
3.  **Manipulation (操作)**: 通过高级语言访问数据，DBMS 自动决定最佳执行策略。

> **关键概念：Physical Data Independence (物理数据独立性)**
> 你不需要知道数据在磁盘上是如何存储的（布局、堆、索引），就可以查询它。

### Definitions (定义)
*   **Relation (关系)**: 一个 **无序集合 (Unordered set)**，包含代表实体属性的关系。
    *   *注意*: 严格来说集合不允许重复元组，但 SQL (Bags) 允许。
*   **Tuple (元组)**: 关系中的一组属性值（通常称为“行”）。
    *   值通常是 **原子的 (Atomic/Scalar)**。
    *   **NULL**: 特殊值，代表“未知”或“未定义”。
*   **Primary Key (主键)**: 唯一标识单个元组。
*   **Foreign Key (外键)**: 链接到另一个关系的主键，用于定义关系（例如 `ArtistAlbum` 链接 `Artist` 和 `Album`）。

---

## 3. Data Manipulation Languages (DML)

用于存储和检索信息的方法。

| 类型 (Type) | 定义 (Definition) | 示例 (Example) |
| :--- | :--- | :--- |
| **Procedural (过程式)** | 查询指定了寻找结果的 **策略/步骤**。<br>(The query specifies the strategy.) | **Relational Algebra**<br>(关系代数) |
| **Non-Procedural (非过程式)** | 查询只指定 **想要什么**，不指定怎么找。<br>(The query specifies only what data is wanted.) | **SQL**<br>(Relational Calculus) |

---

## 4. Relational Algebra (关系代数)

检索和操作元组的基本运算，基于 **集合代数 (Set Algebra)**。
每个操作符的输入和输出都是关系，因此可以 **链式调用 (Chaining)**。

### Basic Operators (基本操作符)

#### 1. Select (σ) - 选择
*   **功能**: 根据谓词 (Predicate) 过滤行。
*   **语法**: `σ_predicate(R)`
*   **SQL 对应**: `WHERE` 子句。

#### 2. Projection (π) - 投影
*   **功能**: 选择特定列，丢弃其他列。
*   **语法**: `π_A1,A2(R)`
*   **SQL 对应**: `SELECT column_name`。

#### 3. Union (∪) - 并集
*   **功能**: 包含出现在任一输入关系中的所有元组。
*   **约束**: 输入关系必须具有 **完全相同的模式 (Schema)**。

#### 4. Intersection (∩) - 交集
*   **功能**: 仅包含同时出现在两个输入关系中的元组。
*   **约束**: 输入关系必须具有 **完全相同的模式**。

#### 5. Difference (-) - 差集
*   **功能**: 在第一个关系中但不在第二个关系中的元组。
*   **约束**: 输入关系必须具有 **完全相同的模式**。

#### 6. Product (×) - 笛卡尔积
*   **功能**: 生成输入关系中元组的所有可能组合。
*   **大小**: `|R| × |S|`。
*   *注意*: 很少单独使用，通常配合 Select 使用。

#### 7. Join (⋈) - 连接
*   **功能**: 基于公共值组合两个关系的元组。
*   **逻辑**: 本质上是 Product (×) 后接 Select (σ)。
*   **语法**: `R ⋈ S`

---

## 5. 💡 Professor's Clarification: Intersection vs. Join
*(针对关于 Intersection 和 Join 区别的重点解答)*

> **Q: Difference between Intersection and Join? Is Join just a flexible intersection?**

这里是关键区别：

1.  **Schema Requirement (模式要求)**
    *   **Intersection (∩)**: 两个关系必须结构 **完全相同**（例如表 A 和表 B 都有 `[id, name]`）。它查找两表中完全一致的行。
    *   **Join (⋈)**: 关系可以结构 **不同**（例如表 A `[artist_id, name]` 和表 B `[artist_id, album]`）。它通过横向拼接创造更“宽”的元组。

2.  **Matching Logic (匹配逻辑)**
    *   **Intersection**: 匹配 **整个元组**（每一列都必须相同）。
    *   **Join**: 仅匹配 **特定属性**（Keys）。

3.  **Result (结果)**
    *   **Intersection**: 返回输入的 **子集**（垂直过滤）。
    *   **Join**: 返回两个输入的 **组合列**（水平扩展）。

---

## 6. Query Optimization Observation (查询优化)

关系代数定义了步骤 (Procedural)，因此顺序对性能至关重要。

*   **方案 A**: `σ_b_id=102(R ⋈ S)` (先连接，再过滤)
*   **方案 B**: `R ⋈ (σ_b_id=102(S))` (先过滤，再连接)

> **结论**: **方案 B 更快**。
> 先过滤数据 (Filtering before joining) 会使中间结果集更小。这正是 SQL 查询优化器 (Query Optimizer) 默默为你做的工作。
