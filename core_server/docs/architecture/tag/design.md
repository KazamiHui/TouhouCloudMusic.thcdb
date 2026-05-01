# Tag

对resource的元描述字段.
数据结构为：
- UID.
内部维护自增.
- 当前的change_node的UID.
- 名称.

---
- [Tag管理](./management/design.md)
- [Tag历史](./history/design.md)
---

## Tag管理
设计文档：[Tag_management](./management/design.md)
对Tag进行管理，包括审核.

## Tag历史
设计文档：[Tag_history](./history/design.md)
记录Tag的变动，单向线性.