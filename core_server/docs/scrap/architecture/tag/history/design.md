# Tag历史

实现tag的历史记录.
change_node的基本结构为：
- UID.
记录当前版本的UID.
- 历史UID.
记录延续的版本的UID.
- UUID.
记录操作涉及的tag对象的UUID.
- 发起者.
记录发起修改请求的账户的UID.
- 审核员.
记录通过修改请求的审核的账户的UID.
- 操作类型.
`create`,``delete`,`add_name`,`delete_name`、`change_parent_tag`中其一.
- 操作内容.
内容见后续操作类型中的内容.

每个change_node以提案为最小单元记录.

---
**操作**
- [获取change_node内容](#获取change_node内容)

**操作类型**
- [create](#create)
- [delete](#delete)
- [add_name](#add_name)
- [delete_name](#delete_name)
- [change_parent_tag](#change_parent_tag)
---

## 获取change_node内容
给定一个UID，获取对应的change_node除UID外的内容.

## create
记录创建的对象的UID.

## delete
记录被删除的tag的所有name.

## add_name
记录新的name.

## delete_name
记录被抛弃的name.

## change_parent_tag
记录被丢弃的父tag的UID.