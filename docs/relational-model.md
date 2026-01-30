# 第一章：关系模型

## 1. 基本概念
**关系模型** (Relational Model) 是用二维表的形式表示实体和实体间联系的数据模型。

> 核心思想：所有数据都存储在表中。

## 2. 关键术语
| 英文 | 中文 | 说明 |
| :--- | :--- | :--- |
| Relation | 关系 | 就是一张表 |
| Tuple | 元组 | 表中的一行（记录） |
| Attribute | 属性 | 表中的一列（字段） |

## 3. SQL 示例
创建一个学生表：

```sql
CREATE TABLE Student (
    ID int PRIMARY KEY,
    Name varchar(255),
    Age int
);
