# API

此处为sever对外接口的描述文档.
## 接口约定
除了以下接口外，所有请求均需要携带账户认证信息.
- /api/user/signin
- /api/resource/search
- /api/resource/data

账户认证信息由`user_signin`签发.

---
- [组织](#组织)
- [账户](#账户)
- [resource](#resource)
- [tag](#tag)
- [审核](#审核)
---

# 组织
接口描述文档：[organization](./organization.md)
登记、停止、恢复一个组织.

# 账户
接口描述文档：[user](./user.md)
注册、登录、登出、注销一个账户.

# resource
接口描述文档：[resource](./resource.md)
搜索、创建、删除、修改一个resource，合并两个resource，获取resource内容.

# tag
接口描述文档：[tag](./tag.md)
搜索、创建、删除一个tag，修改tag的描述.

# 审核
接口描述文档：[audit_or_verify](./audit_or_verify.md)
对resource、tag或用户言论进行审核.