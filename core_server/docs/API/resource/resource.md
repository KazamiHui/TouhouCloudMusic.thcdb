# resource

除`search`外均需要给定语言.

resource结构见[各类resource的结构](../about_resource.md#各类resource的结构).

---
## 目录
- [search](#search)
- [data](#data)
- [new_proposal_modify](#new_proposal_modify)
- [new_proposal_create](#new_proposal_create)
- [new_proposal_remove](#new_proposal_remove)
- [new_proposal_revoke](#new_proposal_revoke)
- [new_proposal_merge](#new_proposal_merge)
---

## search
对resource按指定的条件进行检索.  
**方法**：`POST`.  
**路径**：`/api/v1/resource/search`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `keyword` | string | 是 | - | 检索关键词。 |
| `types` | array[string] | 是 | - | 限定类型。 |
| `filter` | object | 否 | `{}` | 过滤条件（可用项见[可选的过滤条件](../about_resource.md#可选的过滤条件)）。 |
| `sort` | string | 否 | `"personal_recommend:desc"` | 排序，格式 `"field:order"`（可用项见[可选的排序依据](../about_resource.md#可选的排序依据)）。 |
| `extra_info` | array[string] | 否 | `[]` | 除了UUID外需要返回的额外信息，支持`tag`、`intro`、`name_or_title`、`combined_trademark`。 |

### 成功响应 （HTTP 200）
响应体使用`multipart/related`封装.  
使用流式传输.  
首个消息体为`json`，格式如下：
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```
resource字段数据的消息体为`json`，格式如下：
```json
{
    "UUID": "resource的UUID的Content-ID",
    "combined_trademark": "resource的封面的Content-ID",
    "name": "resource的名称的Content-ID",
    "intro": "resource的简介的Content-ID",
    "tag": "resource的tag列表的Content-ID"
}
```
其中除UUID外的项由`extra_info`和该resource具体结构决定存在性.  
所有项的值为resource字段数据的Content-ID.

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**推荐系统未实现且sort使用`personal_recommend`或`public_recommend`**（具有时效性）：
```json
{
    "status": "warning",
    "messages": ["search_unhandled_request"],
    "errorcode": 501
}
```
**`types`含有不存在的resource类型**：
```json
{
    "status": "error",
    "messages": ["unknown_resource_type"],
    "errorcode": 400
}
```
**`types`为空**：
```json
{
    "status": "error",
    "messages": ["unrestricted_resource_type"],
    "errorcode": 400
}
```
**`filter`的元素不是`types`中的resource类型的合法的过滤条件**：
```json
{
    "status": "error",
    "messages": ["request_type_have_not_filter", ["不合规的元素"]],
    "errorcode": 400
}
```

---

## data
获取指定UUID的resource的指定的一些信息.  
**方法**：`POST`.  
**路径**：`/api/v1/resource/data`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | string | 是 | - | 请求的resource的UUID。 |
| `info` | array[string] | 否 | `["name"]` | 需要的resource的信息（可选项见[resource的类型](../about_resource.md#resource的类型)）。 |
| `lang` | string | 是 | - | 返回的resource信息的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `output_data_type` | array[string] | 否 | `["text"]` | 元素可为`text`、`image`，重复出现自动归并。 |
| `history_range` | array | 是（仅当请求`history`字段时） | - | 请求的变更历史的版本数量。长度为`2`，第一个为所在版本（为空表示最新版本），第二个为需要请求的前面的几个版本。 |

### 成功响应 （HTTP 200）
响应体使用`multipart/related`封装，其中首个为json，内容如下：
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "type": "resource的类型",
    "data": {
        "字段1": "字段1数据的Content-ID",
        "字段2": "字段2数据的Content-ID",
        "字段3": "字段3数据的Content-ID",
        "字段4": "字段4数据的Content-ID"
    }
}
```
`data`的项由请求体中的`info`指定包含的内容，字段的值为该字段的数据的Content-ID.  
`history`字段中的`content`的数据项的内容为该操作的数据的Content-ID.

### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**请求的resource不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```
**`lang`的值不存在对应的语言**：
```json
{
    "status": "error",
    "messages": ["unknown_language"],
    "errorcode": 404
}
```
**`output_data_type`有不被支持的类型**：
```json
{
    "status": "error",
    "messages": ["unknown_data_type", ["不被支持的类型"]],
    "errorcode": 400
}
```
**请求的resource的类型不存在`info`中的字段**：
```json
{
    "status": "error",
    "messages": ["unknown_key", ["不存在的字段"]],
    "errorcode": 422
}
```

---

## new_proposal_modify
创建一个`modify resource`的提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**执行权限**：`info_updata`.  
**方法**：`PUT`.  
**路径**：`/api/v1/resource/proposal/new/modify`.

### 请求体
请求体使用`multipart/related`包装，其中首个为json，下表为该json的描述.

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `title`| string | 是 | - | 提案的标题，不超过50个字符。 |
| `intro` | string | 是 | - | 提案的描述，不超过1000个字符。 |
| `language` | string | 是 | - | 提案的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `UUID` | string | 是 | - | 修改的resource的UUID。 |
| `main` | array[object] | 是 | - | 修改resource的内容。 |

`main`字段中元素的格式如下：
```json
{
    "key": "字段名",
    "operation": "操作类型",
    "data": [操作内容]
}
```
其中操作类型可以为`change`、`add`、`delete`、`clear`、`replace`.  
当操作类型为以下时，`data`内的内容如下：
- `change`，第一项为匹配的内容，第二项指定匹配第n项（从`1`开始），第三项为替换的内容.
- `add`，第一项为匹配的内容，第二项指定匹配第n项（从`1`开始），第三项为在匹配的内容之后增添的内容.
- `delete`，第一项为匹配的内容.
- `clear`，没有元素.
- `replace`，第一项为替换的内容.

所有内容的指定都使用Content-ID.  
文本中使用`\UUID:UUID1\`来表示引用UUID对应的resource，`UUID`则是`UUID`字符串.  
其中所有匹配的长度不可超过1024字节。

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
**`title`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["title"]],
    "errorcode": 400
}
```
**`intro`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["intro"]],
    "errorcode": 400
}
```
**resource不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource",["不存在的resource的UUID"]],
    "errorcode": 404
}
```
**`language`的值不存在对应的语言**：
```json
{
    "status": "error",
    "messages": ["unknown_language"],
    "errorcode": 404
}
```
**不存在的操作类型**：
```json
{
    "status": "error",
    "messages": ["unknown_operation_type", ["不存在的操作类型"]],
    "errorcode": 501
}
```
**请求的resource的类型不存在将要修改的字段**：
```json
{
    "status": "error",
    "messages": ["unknown_key", ["不存在的字段"]],
    "errorcode": 422
}
```
**`main`字段的元素中`data`的长度与操作类型不匹配**：
```json
{
    "status": "error",
    "messages": ["illegal_data_length"],
    "errorcode": 400
}
```
**匹配的内容过大**：
```json
{
    "status": "error",
    "messages": ["matching_data_too_large"],
    "errorcode": 403
}
```

---

## new_proposal_create
创建一个`create resource`的提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**执行权限**：`info_updata`.  
**方法**：`PUT`.  
**路径**：`/api/v1/resource/proposal/new/create`.

### 请求体
请求体使用`multipart/related`包装，其中首个为json，下表为该json的描述.

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `title`| string | 是 | - | 提案的标题，不超过50个字符。 |
| `intro` | string | 是 | - | 提案的描述，不超过1000个字符。 |
| `language` | string | 是 | - | 提案的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `type` | string | 是 | - | 创建的resource的类型，可选项见[resource的类型](../about_resource.md#resource的类型)。 |
| `main` | object | 是 | - | 创建的resource（键名为字段，键值为数据块的Content-ID），可选字段根据创建的resource类型决定，各类型字段见[resource的类型](../about_resource.md#resource的类型)。 |
| `extra` | array[object] | 否 | `[]` | 修改其它的resource（元素格式同[new_modify_proposal](#new_modify_proposal)去掉`language`字段）。 |

`extra`字段中可使用`\`转义，`\UUID:self\`表示创建的resource的UUID.  
文本中使用`\UUID:UUID1\`来表示引用UUID对应的resource，`\\UUID`则是`UUID`字符串.

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
**`title`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["title"]],
    "errorcode": 400
}
```
**`intro`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["intro"]],
    "errorcode": 400
}
```
**resource类型不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource_type",["不存在的resource类型的名称"]],
    "errorcode": 404
}
```
**resource类型不存在`main`中的字段名**：
```json
{
    "status": "error",
    "messages": ["unknown_resource_field"],
    "errorcode": 422
}
```
**`language`的值不存在对应的语言**：
```json
{
    "status": "error",
    "messages": ["unknown_language"],
    "errorcode": 404
}
```
余下见[new_modify_proposal](#new_modify_proposal)的错误响应.

---

## new_proposal_remove
创建一个`remove resource`的提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**执行权限**：`info_manager`.  
**方法**：`PUT`.  
**路径**：`/api/v1/resource/proposal/new/remove`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `title`| string | 是 | - | 提案的标题，不超过50个字符。 |
| `intro` | string | 是 | - | 提案的描述，不超过1000个字符。 |
| `language` | string | 是 | - | 提案的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `UUID` | string | 是 | - | 删除的resource的UUID。 |
| `extra` | array[object] | 否 | `[]` | 修改其它的resource（元素格式同[new_modify_proposal](#new_modify_proposal)去掉`language`字段）。 |

文本中使用`\UUID:UUID1\`来表示引用UUID对应的resource，`\\`包裹住的转义为普通文本.

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
**`title`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["title"]],
    "errorcode": 400
}
```
**`intro`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["intro"]],
    "errorcode": 400
}
```
**resource不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```
余下见[new_modify_proposal](#new_modify_proposal)的错误响应.

---

## new_proposal_revoke
创建一个`revoke resource`的提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**执行权限**：`info_manager`.  
**方法**：`PUT`.  
**路径**：`/api/v1/resource/proposal/new/revoke`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `title`| string | 是 | - | 提案的标题，不超过50个字符。 |
| `intro` | string | 是 | - | 提案的描述，不超过1000个字符。 |
| `language` | string | 是 | - | 提案的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `UUID` | string | 是 | - | 回退版本的resource的UUID。 |
| `number` | int | 否 | `1` | 回退的版本数量（当前版本为`0`），范围`1`~`5`。 |
| `extra` | array[object] | 否 | `[]` | 修改其它的resource（元素格式同[new_modify_proposal](#new_modify_proposal)去掉`language`字段）。 |

文本中使用`\UUID:UUID1\`来表示引用UUID对应的resource，`\\UUID`则是`UUID`字符串.

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
**`title`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["title"]],
    "errorcode": 400
}
```
**`intro`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["intro"]],
    "errorcode": 400
}
```
**`number`超出范围**：
```json
{
    "status": "error",
    "messages": ["over_range_revoke_number"],
    "errorcode": 400
}
```
**resource不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```
余下见[new_modify_proposal](#new_modify_proposal)的错误响应.

---

## new_proposal_merge
创建一个`merge resource`的提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**执行权限**：`info_manager`.  
**方法**：`PUT`.  
**路径**：`/api/v1/resource/proposal/new/merge`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `title`| string | 是 | - | 提案的标题，不超过50个字符。 |
| `intro` | string | 是 | - | 提案的描述，不超过1000个字符。 |
| `UUID` | string | 是 | - | 合并的resource的UUID。 |
| `merge_UUID` | string | 是 | - | 被合并的resource的UUID。 |
| `main` | object | 是 | - | resource唯一字段的去留，键名为字段，键值为字符串（只可为`self`或`merge`）。 |
| `extra_language` | string | 是 | - | 合并操作的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `extra` | array[object] | 否 | `[]` | 对合并后resource的修改，格式同[new_modify_proposal](#new_modify_proposal)的`main`字段。 |

`main`中使用`self`代指`UUID`的resource，`merge`代指`merge_UUID`的resource.

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
**`title`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["title"]],
    "errorcode": 400
}
```
**`intro`过长**：
```json
{
    "status": "error",
    "messages": ["over_length",["intro"]],
    "errorcode": 400
}
```
**resource不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource",["不存在的resource的UUID"]],
    "errorcode": 404
}
```
**`main`的键值出现了意外的值**：
```json
{
    "status": "error",
    "messages": ["unknown_value",["存在意外的值的键名"]],
    "errorcode": 404
}
```
余下见[new_modify_proposal](#new_modify_proposal)的错误响应.