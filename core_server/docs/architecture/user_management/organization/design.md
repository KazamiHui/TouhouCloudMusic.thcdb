# 组织

是group的一种，此处分离出来方便进行操作.
具有管理体系，可以对组织下的user进行管理.

---
- [登记](#登记)
- [终止与恢复](#终止与恢复)
---

## 登记
提供一个ID和三个账户注册所需的信息（见[user系统](../user/design.md)下的注册章节）以及他们对应的`leader`,`resource_manager`,`superintendent`中的一个权限.
给定账户权限后直接授予，不经过admin.
可以指定新账户包含的权限，这些权限结合`user权限`（详细请见[用户权限管理系统](../user/manager_permission/design.md)）储存在自身（作为group）的`账户主权限`中.

## 终止与恢复
`leader`提起终止.
`leader`,`resource_manager`,`superintendent`任意一人均可提起恢复.
提起终止的时间即为`停止活动`的时间，恢复时`停止活动`清空.