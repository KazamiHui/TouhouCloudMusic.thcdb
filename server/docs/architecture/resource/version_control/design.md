# 版本控制系统

实现resource的版本控制.
revision_node的基本结构为：
- UID.
记录当前版本的UID.
- 历史UID.
记录延续的版本的UID.
- UUID.
记录操作涉及的resource对象的UUID.
- 发起者.
记录发起修改请求的账户的UID.
- 审核员.
记录通过修改请求的审核的账户的UID.
- 操作类型.
`create`,`modify`,`merge`,`delete`中其一.
- 操作内容.
内容见后续操作类型中的内容.

每个revision_node以提案为最小单元记录.

---
**操作**
- [revoke](#revoke)

**操作类型**
- [create](#create)
- [delete](#delete)
- [modify](#modify)
- [merge](#merge)
---

## revoke
提供一个UUID，按照对应的resource的`当前的revision_node的UID`的内容进行撤销，并将`当前的revision_node的UID`回退上一revision_node的UID.

## create
记录创建的对象的UUID.

## delete
记录被删除的resource的所有值.

## modify
记录修改的键的值的变化范围及其变化内容.

## merge
记录被抛弃的键的值及其所属的resource对象的UUID，和增添的内容.