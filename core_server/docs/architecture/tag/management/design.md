# Tag management系统

对Tag进行管理.

---
## 职能
对Tag的名称进行查改，对Tag进行增删.
- Tag
    - [create](#create)
    - [delete](#delete)
    - 
- 名称
    - [add name](#add-name)
    - [delete name](#delete-name)
- [compliance audit](./compliance_audit/design.md)
---

## create
接收一个名称，这个名称不可为空.

## delete
接收一个UID，这个`UID`必须在表中存在.
清空`UID`对应的tag除`UID`和`当前的change_node的UID`外的所有内容.

## add name
接收以下：
- UID.
- 名称.

## delete name
接收以下：
- UID.
- 名称.

其中，所提供的`名称`在`UID`对应的tag的名称中必须存在，如果是最后一位则报错.

## change parent tag
接收以下：
- UID.
- 父TagUID.
禁止与UID相同.

## compliance audit
设计文档：[compliance_audit](./compliance_audit/design.md)
对Tag的请求提案进行审核，每个提案至多有一个tag对象.