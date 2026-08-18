# 歌单

---
## 目录
- [search](#search)
- [data](#data)
- [create](#create)
- [change](#change)
- [delete](#delete)
- [report_intro](#report_intro)
---

## search
对公开的歌单按指定条件进行检索.  
**方法**：`POST`.  
**路径**：`/api/v1/music_collection/search`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `keyword` | string | 是 | - | 搜索关键词。 |
| `type` | array[string] | 是 | - | 限定歌单内容类型（可选项见[about_resource下的歌曲相关](../../about_resource.md#歌曲相关)）。 |
| `filter` | object | 否 | `{}` | 过滤条件，可用项见下文。 |
| `sort` | string | 否 | `"personal_recommend:desc"` | 排序，格式 `"field:order"`，`field`的可用项为`a-z`、`time`、`personal_recommend`，`order`为`asc`或`desc`。 |
| `extra_info` | array[string] | 否 | `[]` | 除了`UUID`外需要返回的额外信息，支持`ID`、`tag`、`intro`、`name`。 |

`filter`的可用项为`"time":["start","end"]`（时间格式为YYYYMMDD）、`"tag":["tag1的ID","tag2的ID"]`、`"author":[["组织ID", "账户ID"]]`（筛选指定的账户的歌单）.

### 成功响应 （HTTP 200）
响应体使用`multipart/mixed`封装.  
使用流式传输.  
首个消息体为`json`，格式如下：
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```
歌单字段数据的消息体为`json`，格式如下：
```json
{
    "UUID": "歌单的UUID",
    "ID": {
        "org": "歌单所有者所在的组织的ID",
        "profile": "歌单所有者的ID",
        "music_collection": "歌单ID"
    },
    "name": "歌单的名称",
    "intro": "歌单的简介",
    "tag": ["tag1", "tag2"]
}
```
其中除`UUID`外的项由`extra_info`决定存在性.

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**推荐系统未实现且sort使用`personal_recommend`**（具有时效性）：
```json
{
    "status": "warning",
    "messages": ["search_unhandled_request"],
    "errorcode": 501
}
```
**`type`含有不存在的resource类型**：
```json
{
    "status": "error",
    "messages": ["unknown_resource_type"],
    "errorcode": 400
}
```
**`filter`的元素不是合法的过滤条件**：
```json
{
    "status": "error",
    "messages": ["request_type_have_not_filter", ["不合规的元素"]],
    "errorcode": 400
}
```

---

## data
获取指定的歌单的指定的一些信息.  
私有歌单只有所有者可以获取信息，公开歌单所有人均可以获取信息.  
**方法**：`POST`.  
**路径**：`/api/v1/music_collection/data`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | string | 是 | - | 歌单的UUID |
| `ID` | object | 是 | - | 歌单的唯一标识符，格式见下文。 |
| `info` | array[string] | 否 | `["name", "content"]` | 需要的歌单的键的信息，可选项为`name`、`publish`、`content_type`、`tag`、`intro`、`content`。 |

必须提供且只能提供`UUID`或`ID`中的一项.

`ID`字段的格式如下：
```json
{
    "org": "歌单所有者所在的组织的ID",
    "profile": "歌单所有者的ID",
    "music_collection": "歌单ID"
}
```

### 成功响应 （HTTP 200）
使用流式传输.  
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "data": {
        "name": "歌单名称",
        "publish": true,
        "content_type": ["resource类型1", "resource类型2"],
        "tag": ["tag1", "tag2"],
        "intro": "歌单的简介",
        "content": ["UUID1", "UUID2"]
    }
}
```
data的项由请求体中的`info`决定是否存在.

### 错误响应
**含有非法参数（含同时提供`UUID`和`ID`）**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**歌单不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_music_collection"],
    "errorcode": 404
}
```
**请求体中的`info`中含有不存在的信息字段**：
```json
{
    "status": "error",
    "messages": ["unknown_key", ["不存在的键"]],
    "errorcode": 422
}
```

---

## create
创建一个歌单.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
创建公开歌单需要`publish_music_collection`能力.  
**方法**：`PUT`.  
**路径**：`/api/v1/music_collection/create`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `ID` | string | 是 | - | 歌单的唯一标识符。 |
| `name` | string | 是 | - | 歌单的名称，长度不超过50个字符。 |
| `publish` | bool | 是 | - | 歌单是否公开，真为公开，假为私有。 |
| `content_type` | array[string] | 否 | `[]` | 歌单的内容的resource类型限制。 |
| `intro` | string | 否 | `""` | 歌单的简介，长度不超过300字符。 |
| `tag` | array[string] | 否 | `[]` | 歌单的tag。 |
| `content` | array[string] | 是 | - | 歌单的内容，元素的为歌单或resource的UUID。 |

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
**在该账户下存在重复的歌单ID**：
```json
{
    "status": "error",
    "messages": ["repeated_ID"],
    "errorcode": 409
}
```
**不存在的resource类型**：
```json
{
    "status": "error",
    "messages": ["unknown_resource_type"],
    "errorcode": 404
}
```
**不存在的tag**：
```json
{
    "status": "error",
    "messages": ["unknown_tag"],
    "errorcode": 404
}
```
**`content`中的UUID对应的resource或歌单不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource_or_music_collection"],
    "errorcode": 404
}
```
**无权限公开**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**简介过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["introduction"]],
    "errorcode": 400
}
```
**名称过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["name"]],
    "errorcode": 400
}
```

---

## change
改变自身所有的某个歌单的一些字段.  
将歌单公开需要`publish_music_collection`能力.  
**方法**：`PATCH`.  
**路径**：`/api/v1/music_collection/change`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `ID` | string | 是 | - | 歌单的唯一标识符。 |
| `change_content` | object | 是 | - | 修改的内容，字段的数据类型见下表。给出改变的字段及其内容即可。 |

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `new_ID` | string | 歌单的新ID。 |
| `name` | string | 歌单的新名称，长度不超过50个字符。 |
| `publish` | bool | 歌单是否公开，真为公开，假为私有。 |
| `content_type` | array[string] | 歌单的内容的resource类型限制。 |
| `intro` | string | 歌单的简介，长度不超过300个字符。 |
| `tag` | object | 歌单的tag，键名可为`add`、`remove`，表示添加或删去，键值为tag的ID组成的数组。 |
| `content` | array[object] | 歌单的内容，格式见下。 |

`content`中元素的格式如下：
```json
{
    "operation": "可为add或remove",
    "index": "操作的对象索引（首个曲子从1开始，可以为0）",
    "object": "歌单或resource的UUID"
}
```
`object`仅在`operation`为`add`时可用，值会添加在索引内容之后.

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
**请求的歌单不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_music_collection"],
    "errorcode": 404
}
```
#### `content`字段相关
**参数缺失**：
```json
{
    "status": "error",
    "messages": ["content_missing_arg"],
    "errorcode": 400
}
```
**不存在的操作**：
```json
{
    "status": "error",
    "messages": ["content_unknown_operation"],
    "errorcode": 400
}
```
**索引位置不存在**：
```json
{
    "status": "error",
    "messages": ["content_unknown_index"],
    "errorcode": 404
}
```
**`object`对应的resource或歌单不存在**：
```json
{
    "status": "error",
    "messages": ["content_unknown_resource_or_music_collection"],
    "errorcode": 404
}
```
余下见[create](#create)的错误响应.

---

## remove
删除自身拥有的一个歌单.  
**方法**：`PUT`.  
**路径**：`/api/v1/music_collection/remove`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `ID` | string | 是 | - | 歌单的唯一标识符。 |

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
**请求的歌单不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_music_collection"],
    "errorcode": 404
}
```

---

## report_intro
举报歌单的简介.  
**执行权限**：`report`.  
**方法**：`POST`.  
**路径**：`/api/v1/report/music_collection/intro`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | string | 是 | - | 歌单的UUID |
| `ID` | object | 是 | - | 格式见下文。 |
| `reason` | string | 是 | - | 举报的原因，长度不超过80个字符。 |

必须提供且只能提供`UUID`或`ID`中的一项.

`ID`字段的格式如下：
```json
{
    "org": "歌单所有者所在的组织的ID",
    "profile": "歌单所有者的ID",
    "music_collection": "歌单ID"
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
**含有非法参数（含同时提供`UUID`和`ID`）**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**歌单不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_music_collection"],
    "errorcode": 404
}
```
**理由过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["reason"]],
    "errorcode": 400
}
```