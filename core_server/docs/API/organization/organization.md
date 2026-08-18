# 组织

账户的默认能力为`gain_auth`、`info_update`、`publish_music_collection`、`report`.  
组织id预留`admin`和`individual`.  
`individual`组织的账户仅有`info_update`、`publish_music_collection`、`report`能力.

每个账户至多拥有一个身份.
- 组织内只有`leader`可以授予或剥夺同一组织下其他账户`community_manager`、`resource_manager`身份.
- `community_manager`可以授予或剥夺同一组织下其他账户`community_auditor`身份.
- `resource_manager`可以授予或剥夺同一组织下其他账户`resource_checker`、`info_manager`身份.

---
## 目录
- [停止](#停止)
- [恢复](#恢复)
- [获取现有组织列表](#获取现有组织列表)
- [授予身份](#授予身份)
- [剥夺身份](#剥夺身份)
- [加入黑名单](#加入黑名单)
- [移出黑名单](#移出黑名单)
- [获取黑名单](#获取黑名单)
- [授予获得身份的能力](#授予获得身份的能力)
- [剥夺获得身份的能力](#剥夺获得身份的能力)
---

## 停止
报告组织停止活动，通过登录凭证确认操作的组织.  
**权限要求**：`leader`  
**方法**：`PUT`.  
**路径**：`/api/v1/org/activities/cease`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**无权限操作**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**组织已停止**：
```json
{
    "status": "error",
    "messages": ["already_ceased"],
    "errorcode": 409
}
```

---

## 恢复
报告组织恢复活动，通过登录凭证确认操作的组织.  
**权限要求**：`leader`或`resource_manager`或`community_manager`.  
**方法**：`PUT`.  
**路径**：`/api/v1/org/activities/recover`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**无权限操作**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**组织仍活动**：
```json
{
    "status": "error",
    "messages": ["already_living"],
    "errorcode": 409
}
```

---

## 获取现有组织列表
获取所有在活动的组织的列表.  
**方法**：`GET`.  
**路径**：`/api/v1/org/list/living`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "living_list": [
        {
            "id": "第一个组织的id",
            "permission": ["该组织的账户第一个默认能力", "该组织的账户第二个默认能力", ...]
        },
        {
            "id": "第二个组织的id",
            "permission": ["该组织的账户第一个默认能力", "该组织的账户第二个默认能力", ...]
        }
    ]
}
```

---

## 授予权力
授予一个账户指定身份.  
需要经过PoW，难度值为`10`.  
**方法**：`POST`.  
**路径**：`/api/v1/org/authority/grant`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `ID` | string | 是 | - | 要授予身份的账户。 |
| `auth` | string | 是 | - | 要授予的身份，仅可为`community_auditor`、`community_manager`、`resource_checker`、`resource_manager`中的一个。 |

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**无权限进行操作**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**不存在的账户**：
```json
{
    "status": "error",
    "messages": ["unknown_profile"],
    "errorcode": 404
}
```
**不存在的身份**：
```json
{
    "status": "error",
    "messages": ["unknown_authority"],
    "errorcode": 404
}
```
**目标账户已拥有身份**：
```json
{
    "status": "error",
    "messages": ["profile_already_had_authority"],
    "errorcode": 403
}
```

---

## 剥夺身份
剥夺一个账户指定身份.  
需要经过PoW，难度值为`10`.  
**方法**：`POST`.  
**路径**：`/api/v1/org/authority/strip`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `ID` | string | 是 | - | 要剥夺身份的账户。 |
| `auth` | string | 是 | - | 要剥夺的身份，仅可为`community_auditor`、`community_manager`、`resource_checker`、`resource_manager`中的一个。 |

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**无权限进行操作**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**不存在的账户**：
```json
{
    "status": "error",
    "messages": ["unknown_profile"],
    "errorcode": 404
}
```
**不存在的身份**：
```json
{
    "status": "error",
    "messages": ["unknown_authority"],
    "errorcode": 404
}
```
**目标账户未拥有要剥夺的身份**：
```json
{
    "status": "error",
    "messages": ["profile_non_had_authority"],
    "errorcode": 403
}
```

---

## 加入黑名单
黑名单功能为禁止账户获取指定身份的能力.  
需要经过PoW，难度值为`10`.  
加入黑名单会同步剥夺该账户的对应身份.  
**权限要求**：`community_manager`或`resource_manager`.  
**方法**：`POST`.  
**路径**：`/api/v1/org/authority/blacklist/add`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `ID` | string | 是 | - | 要加入黑名单的账户。 |
| `type` | string | 是 | - | 要加入的黑名单的类型，可选值根据请求者决定。 |

- 如果请求者身份为`community_manager`，则`type`可选`community_auditor`.
- 如果请求者身份为`resource_manager`，则`type`可选`resource_checker`和`info_manager`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**无权限进行操作**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**不存在的身份**：
```json
{
    "status": "error",
    "messages": ["unknown_authority"],
    "errorcode": 422
}
```
**不存在的账户**：
```json
{
    "status": "error",
    "messages": ["unknown_profile"],
    "errorcode": 404
}
```
**目标账户不为`community_auditor`或`resource_checker`**：
```json
{
    "status": "error",
    "messages": ["illegal_target_profile"],
    "errorcode": 403
}
```
**目标账户已在黑名单**：
```json
{
    "status": "error",
    "messages": ["profile_already_in_blacklist"],
    "errorcode": 403
}
```

---

## 移出黑名单
黑名单功能为禁止账户获取指定身份的能力.  
需要经过PoW，难度值为`10`.  
**权限要求**：`community_manager`或`resource_manager`.  
**方法**：`POST`.  
**路径**：`/api/v1/org/authority/blacklist/remove`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `ID` | string | 是 | - | 要移出黑名单的账户。 |
| `type` | string | 是 | - | 要加入的黑名单的类型，可选值根据请求者决定。 |

- 如果请求者身份为`community_manager`，则`type`可选`community_auditor`.
- 如果请求者身份为`resource_manager`，则`type`可选`resource_checker`和`info_manager`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**无权限进行操作**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**不存在的身份**：
```json
{
    "status": "error",
    "messages": ["unknown_authority"],
    "errorcode": 422
}
```
**不存在的账户**：
```json
{
    "status": "error",
    "messages": ["unknown_profile"],
    "errorcode": 404
}
```
**目标账户不在黑名单**：
```json
{
    "status": "error",
    "messages": ["profile_non_in_blacklist"],
    "errorcode": 403
}
```

---

## 获取黑名单
黑名单功能为禁止账户获取指定身份的能力.

- 请求者身份为`community_manager`则获取`community_auditor`身份黑名单；
- 请求者身份为`resource_manager`则获取`resource_checker`和`info_manager`身份黑名单；
- 请求者身份为`leader`则获取`resource_checker`、`community_auditor`和`info_manager`身份黑名单.

**权限要求**：`community_manager`或`resource_manager`或`leader`.  
**方法**：`GET`.  
**路径**：`/api/v1/org/authority/blacklist/data`.

### 成功响应 （HTTP 200）
使用流式传输.
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "blacklist": [
        {
            "type": "黑名单类型（community_auditor、resource_checker或info_manager）",
            "content": [
                "账户ID"
            ]
        }
    ]
}
```

---

## 授予获得身份的能力
授予指定账户`gain_auth`能力.  
需要经过PoW，难度值为`10`.  
**权限要求**：`community_manager`或`resource_manager`或`leader`.  
**方法**：`POST`.  
**路径**：`/api/v1/org/user/permission/grant/gain_auth`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `ID` | string | 是 | - | 要授予获得身份的能力的账户。 |

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**无权限进行操作**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**不存在的账户**：
```json
{
    "status": "error",
    "messages": ["unknown_profile"],
    "errorcode": 404
}
```
**目标账户已具有能力**：
```json
{
    "status": "error",
    "messages": ["profile_already_had_permission"],
    "errorcode": 403
}
```

---

## 剥夺获得身份的能力
剥夺指定账户`gain_auth`能力.  
需要经过PoW，难度值为`10`.  
**权限要求**：`leader`.  
**方法**：`POST`.  
**路径**：`/api/v1/org/user/permission/strip/gain_auth`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `ID` | string | 是 | - | 要剥夺获得身份的能力的账户。 |

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**无权限进行操作**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**不存在的账户**：
```json
{
    "status": "error",
    "messages": ["unknown_profile"],
    "errorcode": 404
}
```
**目标账户不具有能力**：
```json
{
    "status": "error",
    "messages": ["profile_non_had_permission"],
    "errorcode": 403
}
```