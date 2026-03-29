# User管理系统

该系统作为基础设施，用于管理user.

---
- [group](#group)
- [User](./user/design.md)
- [组织](./organization/design.md)
---

## group
User组成的集合，表键包含：
- UID.
内部维护自增.
- ID.
由请求指定.
- 停止活动.
- 账户主权限.
满9个月则对其中所有账户进行销户，满一年将自身ID置空并将其中所有账户执行即刻销户（与销户满90天的行为相同）.

group具有一定对于成员的规则.
### 预留
`admin`，成员只有一个admin账户，UID为0.
`individual`，`账户主权限`为`resource_rectify`,`create_personal_list`权限，没有管理权限，UID为1.

## User
设计文档：[user](./user/design.md)
必须在某个group下.

## 组织
设计文档：[organization](./organization/design.md)
包含一系列User，group的一种.
具有管理体系，创建时必须提供`leader`,`resource_manager`,`superintendent`.