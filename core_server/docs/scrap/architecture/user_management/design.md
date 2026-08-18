# User管理系统

该系统作为基础设施，用于管理user.

---
- [group](./group/design.md)
- [User](./user/design.md)
---

```mermaid
sequenceDiagram
Title: 账户管理
participant user as 用户
participant profile as 账户
participant sql as 数据库

user ->> profile: 注册

profile ->> sql: 查询group
sql -->> profile: group的信息
profile ->> sql: 查询ID唯一性
sql -->> profile: 布尔值，真表示唯一，假表示不唯一

alt 账户ID不唯一或group不存在
profile -->> user: 拒绝注册
end
```

---
## group
设计文档：[group](./group/design.md)
User组成的集合.

## User
设计文档：[user](./user/design.md)  
必须在某个group下.