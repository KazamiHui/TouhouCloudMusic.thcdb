# resource

对resource的结构进行描述，每个resource类别都有独立的表.
每个resource结构头部均有：
- UUID.
- 当前的revision_node的UID.

---
**功能**
- [获取数据](#获取数据)
- [resource版本控制系统](./version_control/design.md)

**类别**
- [文化作品创作者](./artist/design.md)
- [活动](./event/design.md)
- [社团、公司](./organization/design.md)
- [产品](./release/design.md)
- [歌曲](./song/design.md)

**元描述**
- [Tag](../tag/design.md)
---

## 获取数据
给定一个UUID，输出除UUID的数据.

## resource版本控制系统
设计文档：[resource_version_control](./version_control/design.md).
提供对resource的版本控制，可以查看变更历史.