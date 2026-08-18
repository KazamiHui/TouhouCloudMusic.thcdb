# resource

对resource的结构进行描述，每个resource类别都有独立的表.  
每个resource结构头部均有：
- UUID.
- 当前的revision_node的UID.
- 相关非文本内容.
结构为键值对，含有的键见各类resource定义，值根据具体实践决定，但是必须明确媒体类型.

---
**功能**
- [管理系统](./management/design.md)

**类别**
- [文化作品创作者](./types/artist/design.md)
- [活动](./types/event/design.md)
- [社团、公司](./types/team/design.md)
- [产品](./types/release/design.md)
- [歌曲](./types/song/design.md)

**元描述**
- [Tag](../tag/design.md)

**辅助结构**
- [自然语种内容表](./language/design.md)
---

## 管理系统
设计文档：[resource_management](./management/design.md)
该系统作为基础设施，对resource进行管理.