# API

此处为core_server对外接口的描述文档.

## 接口约定
### 特殊接口
所有请求均需要在header以`signin_profile_token:{登录凭证}`格式携带账户登录凭证并使用HTTPS协议，特例见该部分后续约定.  
以下接口外不需要携带账户登录凭证.
- `/api/v1/org/list/living`.
- `/api/v1/user/signup`.
- `/api/v1/user/signin`.
- `/api/v1/user/freeze`.
- `/api/v1/user/unfreeze`.
- `/api/v1/resource/search`.
- `/api/v1/resource/data`.
- `/api/v1/music_collection/search`.
- `/api/v1/music_collection/data`.
- `/api/v1/tag/search`.

以下接口可用HTTP或HTTPS，HTTP请求必须抹去header的账户登录凭证.
- `/api/v1/resource/search`.
- `/api/v1/resource/data`.
- `/api/v1/music_collection/search`.
- `/api/v1/music_collection/data`.
- `/api/v1/tag/search`.

如果不抹去则会报错以下：
```json
{
    "status": "error",
    "messages": ["illegal_header"],
    "errorcode": 400
}
```

### 凭证相关
账户登录凭证由`user_signin`接口签发，有效期24h.  
账户登录凭证失效后请求需要登录凭证的接口返回以下：
```json
{
    "status": "error",
    "messages": ["unknown_token"],
    "errorcode": 401
}
```

### 请求体
无特殊说明，请求体默认为`json`.
禁止包含SQL语句，对于任何包含SQL语句的请求将会报错以下：
```json
{
    "status": "error",
    "messages": ["illegal_request"],
    "errorcode": 403
}
```

---
- [组织](#组织)
- [账户](#账户)
- [resource](#resource)
- [tag](#tag)
- [审核](#审核)
- [PoW](#PoW)
---

# 组织
接口描述文档：[organization](./organization/organization.md)  
停止、恢复一个组织，管理组织内账户的权限.

# 账户
接口描述文档：[user](./user/user.md)  
注册、登录、登出、冻结、解冻、注销一个账户.  
以账户为身份对歌单、关注进行操作.  
获取关于自身账户的通知.

# resource
接口描述文档：[resource](./resource/resource.md)  
相关文档：[about_resource](./about_resource.md)  
搜索、创建、删除、修改一个resource，合并两个resource，获取resource的指定数据.

# tag
接口描述文档：[tag](./tag/tag.md)  
搜索、创建、删除一个tag，修改tag的描述.

# 审核
接口描述文档：[examine](./examine/examine.md)  
对信息变动的提案进行审核.

# PoW
接口描述文档：[PoW](./PoW/PoW.md)  
获取难题，证明自身不是恶意攻击，用于一些接口.

# 运维相关
接口文档：[maintenance](./maintenance/README.md)  
用于内部维护，不对外开放.