# 组织

具有管理体系，可以对组织下的user进行管理.

---
- [登记](#登记)
- [终止与恢复](#终止与恢复)
---

## 登记
提供一个ID和三个账户注册所需的信息（见[user系统](../user/design.md)下的注册章节）以及他们对应的`leader`,`resource_manager`,`superintendent`中的一个权限.
给定账户权限后直接授予，不经过admin.

## 终止与恢复
`leader`提起终止.
`leader`,`resource_manager`,`superintendent`任意一人均可提起恢复.
提起终止的时间即为`停止活动`的时间，恢复时`停止活动`清空.