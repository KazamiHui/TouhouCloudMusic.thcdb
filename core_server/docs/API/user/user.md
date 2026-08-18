# 账户

除了`freeze`和`unfreeze`之外，从header确定操作的账户.  
强制使用HTTPS.

---
## 目录
- [signup](#signup)
- [signin](#signin)
- [signout](#signout)
- [freeze](#freeze)
- [unfreeze](#unfreeze)
- [close](#close)
- [get_questions_list](#get_questions_list)
- [change_info](#change_info)
- [行为](#行为)
---

## signup
注册一个账户，不需要登录凭证.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`6`.  
**方法**：`PUT`.  
**路径**：`/api/v1/user/signup`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `org_id` | string | 是 | - | 归属的组织的ID，不可为`admin`。 |
| `profile_id` | string | 是 | - | 注册的账户的ID，不超过20个字符。 |
| `password` | string | 是 | - | 注册的账户的密码。 |
| `Q_and_A` | object | 是 | - | 用于后续解冻、冻结、注销、恢复等操作。键名为题目，键值为答案。至少一组。 |

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
**不存在的group**：
```json
{
    "status": "error",
    "messages": ["unknown_group", ["不存在的group"]],
    "errorcode": 404
}
```
**在指定group下存在重复的账户ID**：
```json
{
    "status": "error",
    "messages": ["repeated_ID"],
    "errorcode": 409
}
```
**账户ID过长**：
```json
{
    "status": "error",
    "messages": ["over_length", ["profile_id"]],
    "errorcode": 400
}
```
**缺少Q&A**：
```json
{
    "status": "error",
    "messages": ["missing_Q_and_A"],
    "errorcode": 422
}
```

---

## signin
登录一个账户.  
登录视为找回账户.  
**方法**：`PUT`.  
**路径**：`/api/v1/user/signin`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `org_id` | string | 是 | - | 归属的组织的ID，不可为`admin`。 |
| `profile_id` | string | 是 | - | 登录的账户的ID。 |
| `password` | string | 是 | - | 登录的账户的密码。 |

### 成功响应 （HTTP 200）
登陆凭证为JWT，有效期24h.
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "token": "登录凭证"
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
**登录失败**：
```json
{
    "status": "error",
    "messages": ["failed_signin"],
    "errorcode": 401
}
```

---

## signout
登出一个账户.  
**方法**：`PUT`.  
**路径**：`/api/v1/user/signout`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `password` | string | 是 | - | 登出的账户的密码。 |

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
**密码错误**：
```json
{
    "status": "error",
    "messages": ["wrong_password"],
    "errorcode": 401
}
```

---

## freeze
冻结一个账户.  
**方法**：`PUT`.  
**路径**：`/api/v1/user/freeze`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `org_id` | string | 是 | - | 归属的组织的ID，不可为`admin`。 |
| `profile_id` | string | 是 | - | 冻结的账户的ID。 |
| `Q_and_A` | object | 是 | - | 键名为题目序号，键值为答案。至少给出一个，给出的A必须全部和题目序号的A对应。 |

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
**冻结失败**：
```json
{
    "status": "error",
    "messages": ["failed_freeze"],
    "errorcode": 401
}
```

---

## unfreeze
解冻一个账户.  
**方法**：`PUT`.  
**路径**：`/api/v1/user/unfreeze`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `org_id` | string | 是 | - | 归属的组织的ID，不可为`admin`。 |
| `profile_id` | string | 是 | - | 解冻的账户的ID。 |
| `password` | string | 是 | - | 解冻的账户的密码。 |
| `Q_and_A` | object | 是 | - | 键名为题目序号，键值为答案。至少给出一个，给出的A必须全部和题目序号的A对应。 |

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
**解冻失败**：
```json
{
    "status": "error",
    "messages": ["failed_unfreeze"],
    "errorcode": 401
}
```

---

## close
注销一个账户，90天内可找回，超出清空所有字段仅留UUID.  
**方法**：`PUT`.  
**路径**：`/api/v1/user/close`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `password` | string | 是 | - | 注销的账户的密码。 |
| `Q_and_A` | object | 是 | - | 键名为题目序号，键值为答案。必须给出全部，给出的A必须全部和题目序号的A对应。 |

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
**注销失败**：
```json
{
    "status": "error",
    "messages": ["failed_close"],
    "errorcode": 401
}
```

---

## get_questions_list
获取账户的`Q&A`的Q列表.  
**方法**：`GET`.  
**路径**：`/api/v1/user/list/questions`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "questions_list": [
        {
            "number": 1,
            "content": "题目1内容"
        },
        {
            "number": 2,
            "content": "题目2内容"
        }
    ]
}
```

---

## change_info
修改账户一些信息.  
**方法**：`PATCH`.  
**路径**：`/api/v1/user/change/info`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `Q_and_A` | object | 是 | - | 键名为题目序号，键值为答案。必须给出全部，给出的A必须全部和题目序号的A对应。 |
| `info` | string | 是 | - | 需要修改的信息。 |
| `content` | 由`info`决定 | 是 | - | 修改的内容。 |

`content`的数据类型由`info`决定，对于`info`的取值有以下情况：
- `ID`或`password`.  
`content`的数据类型为string.  
- `Q_and_A`.  
`content`的数据类型为array[object].  

对于`info`为`Q_and_A`的情况，`content`中的object的格式为以下中的一个：
- change，修改题目的内容和答案.  
```json
{
    "operation": "change",
    "Q_numbering": 题目编号,
    "新的题目": "新的题目的答案"
}
```
- add，添加一个题目.  
```json
{
    "operation": "add",
    "新的题目": "新的答案"
}
```
- delete，删除一个题目.  
```json
{
    "operation": "delete",
    "Q_numbering": 题目编号,
}
```


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
**不存在的信息**：
```json
{
    "status": "error",
    "messages": ["unknown_info", ["不存在的信息的名称"]],
    "errorcode": 404
}
```
**存在错误答案**：
```json
{
    "status": "error",
    "messages": ["wrong_answer"],
    "errorcode": 422
}
```
**（如果请求体中`info`为ID）所在的group中ID已被占用**：
```json
{
    "status": "error",
    "messages": ["repeated_ID"],
    "errorcode": 409
}
```

---

## 子模块
包含以下模块的接口：
- [关注](./attention/attention.md)
用于管理账户对于resource的关注.
- [歌单](./music_collection/music_collection.md)
用于账户管理自身的歌单和举报歌单简介.
- [通知](./notice/notice.md)
用于查看通知.