# examine

对于事务类型，它们对应的事务对象类型如下：
- `appeal`.
    - `affair`.
- `report`.
    - `affair_comment`.
    - `music_collection_intro`.
- `proposal`.
    - `resource`.
    - `tag`.

---
## 目录
- [affair_list](#affair_list)
- [affair_details](#affair_details)
- [approve](#approve)
- [reject](#reject)
- [create_comment](#create_comment)
- [remove_comment](#remove_comment)
- [report_comment](#report_comment)
- [appeal](#appeal)
---

## affair_list
获取事务列表.  
`resource_checker`账户只可以获取到`resource`的提案；`community_auditor`可以获取到标签的提案、歌单简介的举报、事务评论的举报和上诉.  
**执行权限**：`resource_checker`或`community_auditor`.  
**方法**：`GET`.  
**路径**：
- 连接：`/api/v1/examine/affair/list/{参数}`.
    路径中参数可选以下，参数组合使用`&`连接：
    - `size={接收量}`，不提供该项则为`8`.
    - `sort={排序依据}`，排序依据可选`time`、`intro_size`.
    - `UUID={resource的UUID}`，专用于`resource`提案，指定`resource`.
- 断开：`/api/v1/examine/affair/list/disconnect`.

### 成功响应（HTTP200）
使用SSE连接，event为`removed`、`unprocessed`，id为事务UUID.  
`unprocessed`类型的event的data的格式如下：
```json
{
    "UUID": "事务UUID",
    "type": ["事务类型（appeal、proposal、report）", "事务对象类型（resource、tag、music_collection_intro、affair_comment、affair）"],
    "target": "对象的UUID",
    "title": "事务标题",
    "intro": "事务说明"
}
```
`title`项只对`resource`的提案存在.

`removed`类型的event的data的格式如下：
```json
{
    "UUID": "事务UUID"
}
```

如果没有新事务将会根据路径参数中的`sort`正序填满`size`指定的接受量.  
重连后提供事务UUID和已接收的量从该事务继续按`sort`正序填满`size`指定的接受量.

---

## affair_details
获取事务的详细信息.  
**执行权限**：`resource_checker`或`community_auditor`.  
**方法**：`GET`.  
**路径**：`/api/v1/examine/affair/details/{事务UUID}`.

### 成功响应（HTTP200）
响应体使用`multipart/form-data`封装，其中首个为json，name为`main`，数据体如下：
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "affair":{
        "type": ["事务类型（appeal、proposal、report）", "事务对象类型（resource、tag、music_collection_intro、affair_comment、affair）"],
        "status": ["事务状态（processed、unprocessed）", ["处理结果（approve、punish）", "处罚"]]
    }
}
```
其中`"处罚"`只在举报类型事务存在，格式见[approve接口](#approve)。

事务讨论为json，name为`comments`，数据体如下：
```json
[
    {
        "ID": "评论的ID",
        "content": "该条评论的内容",
        "time": "评论创建的时间",
        "children": [
            "子评论的ID"
        ]
    }
]
```
事务详情为json，name为`details`，数据体见后文具体事务类型.
#### resource提案
##### modify类型
```json
{
    "title": "提案标题",
    "intro": "提案简介",
    "target": "变动的resource的UUID",
    "parent": "父事务的UUID",
    "change": {
        "字段1（纯文本）": {
            "operation": "操作类型（insert、delete或replace）",
            "index": "位置（第一个字符为1）",
            "content": "变动内容的name"
        },
        "字段2（非文本）": "新内容的name"
    }
}
```
`parent`字段对独立的modify提案不存在.  
字段是否为纯文本请见[about_resource](../about_resource.md)，此处给出两种类型的示例.
##### revoke类型
```json
{
    "title": "提案标题",
    "intro": "提案简介",
    "target": "变动的resource的UUID",
    "change": {
        "字段1（纯文本）": {
            "operation": "操作类型（insert、delete或replace）",
            "index": "位置（第一个字符从1开始）",
            "content": "变动内容的name"
        },
        "字段2（非文本）": "新内容的name"
    }
}
```
字段是否为纯文本请见[about_resource](../about_resource.md)，此处给出两种类型的示例.
##### merge类型
```json
{
    "title": "提案标题",
    "intro": "提案简介",
    "target": ["合并的第一个resource的UUID", "合并的第二个resource的UUID"],
    "change": {
        "字段1（纯文本）": {
            "operation": "操作类型（insert、delete或replace）",
            "index": "位置（第一个字符从1开始）",
            "content": "变动内容的name"
        },
        "字段2（非文本）": "新内容的name"
    }
}
```
字段是否为纯文本请见[about_resource](../about_resource.md)，此处给出两种类型的示例.  
`change`为对于合并后结果的变更.
##### create类型
```json
{
    "title": "提案标题",
    "intro": "提案简介",
    "content": {...}
}
```
`content`为被创建的resource的内容，结构请见[about_resource](../about_resource.md).  
其中每个字段的值为该字段数据的name.
##### remove类型
```json
{
    "title": "提案标题",
    "intro": "提案简介",
    "target": "变动的resource的UUID"
}
```
#### 标签的提案
```json
{
    "intro": "提案简介",
    "target": {
        "Path": "变动的标签所在路径",
        "ID": "变动的标签的ID（操作类型为create时无需提供该项）"
    },
    "operation": "操作类型（create、remove、add_name、delete_name）",
    "name_list": "变动的名字列表"
}
```
#### 歌单简介的举报
```json
{
    "intro": "举报简介",
    "target": "被举报的歌单的UUID"
}
```
#### 提案评论的举报
```json
{
    "intro": "举报的描述",
    "target": {
        "ID": "评论的ID",
        "publisher": {
            "org_id": "发布评论的账户所在组织的ID",
            "profile_id": "发布评论的账户的ID"
        },
        "content": "该条评论的内容",
        "time": "评论创建的时间",
        "parent": "父评论的ID"
    },
    "content": [
        {
            "ID": "评论的ID",
            "content": "该条评论的内容",
            "time": "评论创建的时间",
            "children": [
                "子评论的ID"
            ]
        }
    ]
}
```
`content`不含`target`中的评论.
#### 上诉
```json
{
    "reason": "上诉理由",
    "target": "被剥夺的能力",
    "content": "上诉事务UUID"
}
```

### 错误响应
**事务不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair"],
    "errorcode": 404
}
```

---

## approve
通过事务并进行处理.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`6`.  
**执行权限**：`resource_checker`或`community_auditor`.  
**方法**：`PUT`.  
**路径**：`/api/v1/examine/affair/approve`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `target` | string | 是 | - | 通过的事务的UUID。 |
| `punish` | array | 否 | ` ` | 对被举报的账户做出处罚，为空则表示不做处罚。仅适用于举报类型的事务。 |

`punish`中的可选键值对及其效果有以下.（后续可能拓展）
#### 剥夺能力
```json
"permission": "能力名"
```
只在`music_collection_intro_report`可用，可选值为`publish_music_collection`.（后续可能拓展）
#### 创建通知
```json
"notice": {
    "receiver_auth": "接收者的身份（resource_manager、community_manager）"
}
```
该项必须配合至少一个处罚.
#### 删除对象
```json
"remove"
```
只在`affair_comment_report`可用.

### 成功响应（HTTP200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**事务不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair"],
    "errorcode": 404
}
```
**`punish`不应存在**：
```json
{
    "status": "error",
    "messages": ["unknown_punish"],
    "errorcode": 422
}
```
**`punish`结构非法**：
```json
{
    "status": "error",
    "messages": ["illegal_punish"],
    "errorcode": 422
}
```
**事务已被处理**：
```json
{
    "status": "error",
    "messages": ["affair_already_processed"],
    "errorcode": 404
}
```

---

## reject
驳回事务.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`6`.  
**执行权限**：`resource_checker`或`community_auditor`.  
**方法**：`PUT`.  
**路径**：`/api/v1/examine/affair/reject`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `target` | string | 是 | - | 驳回的事务的UUID。 |
| `reason` | string | 是 | - | 驳回的理由，不超过1000个字符。 |

### 成功响应（HTTP200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**事务不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair"],
    "errorcode": 404
}
```
**未提供理由**：
```json
{
    "status": "error",
    "messages": ["missing_reason"],
    "errorcode": 422
}
```
**理由过长**：
```json
{
    "status": "error",
    "messages": ["over_length", ["reason"]],
    "errorcode": 400
}
```

---

## create_comment
创建一个事务评论.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**执行权限**：`resource_checker`或`community_auditor`.  
**方法**：`POST`.  
**路径**：`/api/v1/examine/affair/comment/create`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `UUID` | string | 是 | - | 评论的事务。 |
| `parent`| string | 否 | `0` | 父评论，`0`表示作为顶级评论。 |
| `content` | string | 是 | - | 评论内容，长度不超过3000字符。 |

### 成功响应（HTTP200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**事务不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair"],
    "errorcode": 404
}
```
**作为父评论的事务评论不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair_comment"],
    "errorcode": 404
}
```
**内容为空**：
```json
{
    "status": "error",
    "messages": ["missing_content"],
    "errorcode": 422
}
```
**内容过长**：
```json
{
    "status": "error",
    "messages": ["over_length", ["content"]],
    "errorcode": 400
}
```

---

## remove_comment
删除自身的一个事务评论.  
将会删除其下所有子评论.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**执行权限**：`resource_checker`或`community_auditor`.  
**方法**：`POST`.  
**路径**：`/api/v1/examine/affair/comment/remove`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `UUID` | string | 是 | - | 事务评论的所在的事务。 |
| `comment_ID`| string | 是 | - | 要删除的评论的ID。 |

### 成功响应（HTTP200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**事务不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair"],
    "errorcode": 404
}
```
**要删除的事务评论不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair_comment"],
    "errorcode": 404
}
```
**要删除的事务评论不属于请求者**：
```json
{
    "status": "error",
    "messages": ["affair_comment_is_not_yourself"],
    "errorcode": 422
}
```

---

## report_comment
举报一个事务评论.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**执行权限**：`resource_checker`或`community_auditor`.  
**方法**：`POST`.  
**路径**：`/api/v1/examine/affair/comment/report`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `reason` | string | 是 | - | 举报理由，长度不超过800字符。 |
| `UUID` | string | 是 | - | 事务评论的所在的事务。 |
| `comment_ID`| string | 是 | - | 要举报的评论的ID。 |

### 成功响应（HTTP200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**事务不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair"],
    "errorcode": 404
}
```
**要举报的事务评论不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_affair_comment"],
    "errorcode": 404
}
```
**理由过长**：
```json
{
    "status": "error",
    "messages": ["over_length", ["reason"]],
    "errorcode": 400
}
```

---

## appeal
对七天内被剥夺能力的事务处理结果进行上诉.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**方法**：`POST`.  
**路径**：`/api/v1/examine/appeal/create`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `reason` | string | 是 | - | 上诉理由，长度不超过800字符。 |
| `UUID` | string | 是 | - | 上诉的事务。 |

### 成功响应（HTTP200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**事务不存在或事务与请求者无关**：
```json
{
    "status": "error",
    "messages": ["unknown_affair"],
    "errorcode": 404
}
```
**未被剥夺能力**：
```json
{
    "status": "error",
    "messages": ["illegal_appeal"],
    "errorcode": 403
}
```
**已超过上诉期限**：
```json
{
    "status": "error",
    "messages": ["appeal_timeout"],
    "errorcode": 403
}
```
**理由过长**：
```json
{
    "status": "error",
    "messages": ["over_length", ["reason"]],
    "errorcode": 400
}
```