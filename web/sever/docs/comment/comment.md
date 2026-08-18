# comment

---
## 目录
- [create](#create)
- [data](#data)
- [report](#report)
- [delete](#delete)
---

## create
创建一个评论，关于resource或提案.  
**方法**：`PUT`.  
**路径**：`/api/v1/comment/create`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `object_type` | string | 是 | - | 评论对象的类型。 |
| `ID` | string | 是 | - | 评论对象的唯一标识符。 |
| `content` | string | 是 | - | 评论的内容，长度6000字以内。 |

评论对象类型有`resource`和`proposal`开头的两种，按照各自领域细分.  
`proposal`有且只有`resource`、`tag`、`socialize`三种，`resource`请见[about_resource](./about_resource.md).

### 响应
#### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```
#### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**未知的对象类型**：
```json
{
    "status": "error",
    "messages": ["unknown_object_type"],
    "errorcode": 404
}
```
**对象不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_object"],
    "errorcode": 404
}
```
**评论过长**：
```json
{
    "status": "error",
    "messages": ["over_length", ["content"]],
    "errorcode": 400
}
```

---

## data
查看一个评论（及其子评论）的指定字段数据，使用SSE，事件流格式，返回完所有评论后连接关闭.  
**方法**：`POST`.  
**路径**：`/api/v1/comment/data`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `type` | string | 是 | - | 评论的类型，有且只有`proposal`和`resource`。 |
| `ID` | string | 是 | - | 评论的唯一标识符。 |
| `key` | array[string] | 否 | `["content"]` | 需要的评论的键值，有`publisher`、`time`、`content`三者可选，`publisher`只有resource类型存在。 |
| `sub_comment_depth` | int | 否 | `-1` | 获取的子评论深度，范围`-1`~`4`。`-1`表示不获取子评论，`0`表示只获取子评论ID，正整数表示获取到第几层子评论内容。 |

### 响应
#### 成功响应 （HTTP 200）
以下为第一个消息体内容.
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```
以下为后续data载荷内容，每个评论一个消息体.
```json
{
    "ID": "评论的ID",
    "publisher": "发布者的UID",
    "content": "评论内容",
    "time": "UNIX时间戳",
    "sub_comment": ["子评论1的ID", "子评论2的ID"]
}
```
其中，评论的项中的`publisher`、`content`、`time`由请求体中的`key`决定是否存在，此处将三项全部写出.  
评论的`sub_comment`项由请求体中的`sub_comment_depth`决定是否存在，`-1`则不存在.
#### 错误响应
**含有非法参数（含`sub_comment_depth`不在合法范围内）**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**不存在的评论类型**：
```json
{
    "status": "error",
    "messages": ["unknown_comment_type"],
    "errorcode": 400
}
```
**评论不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_comment"],
    "errorcode": 400
}
```
**请求的键不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_key"],
    "errorcode": 400
}
```

---

## report
创建一个关于这个评论的举报.  
**方法**：`POST`.  
**路径**：`/api/v1/report/comment`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `type` | string | 是 | - | 举报的评论的类型，有且只有`proposal`和`resource`。 |
| `ID` | string | 是 | - | 举报的评论的唯一标识符。 |
| `reason` | string | 是 | - | 举报的原因，长度80字以内。 |

### 响应
#### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```
#### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**不存在的评论类型**：
```json
{
    "status": "error",
    "messages": ["unknown_comment_type"],
    "errorcode": 400
}
```
**评论不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_comment"],
    "errorcode": 400
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

## delete
删除一个评论（及其所有子评论）.  
**方法**：`PUT`.  
**路径**：`/api/v1/comment/delete`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `type` | string | 是 | - | 评论的类型，有且只有`proposal`和`resource`。 |
| `ID` | string | 是 | - | 评论的唯一标识符。 |

### 响应
#### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```
#### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**不存在的评论类型**：
```json
{
    "status": "error",
    "messages": ["unknown_comment_type"],
    "errorcode": 400
}
```
**评论不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_comment"],
    "errorcode": 400
}
```