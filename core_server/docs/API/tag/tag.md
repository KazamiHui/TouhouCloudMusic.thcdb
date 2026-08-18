# 标签

tag路径使用tag的ID作为标识，用`/`作为分隔符。

---
## 目录
- [search](#search)
- [new_proposal_create](#new_proposal_create)
- [new_proposal_remove](#new_proposal_remove)
- [new_proposal_add_name](#new_proposal_add_name)
- [new_proposal_delete_name](#new_proposal_delete_name)
---

## search
检索标签列表.  
**方法**：`POST`.  
**路径**：`/api/v1/tag/search`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `keyword` | string | 是 | - | 检索关键词。 |
| `lang` | string | 是 | - | 返回内容的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `path` | string | 否 | `"/"` | 检索指定路径下的标签。 |
| `depth` | int | 否 | `1` | 检索的深度。 |

### 成功响应 （HTTP 200）
使用`multipart/mixed`包装.  
使用流式传输.

首个消息体为`json`，格式如下：
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```
tag字段数据的消息体为`json`，格式如下：
```json
{
    "path": "标签所在的路径",
    "UUID": "标签的UUID",
    "name": ["标签的名字1", "标签的名字2"]
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
**`lang`的值不存在对应的语言**：
```json
{
    "status": "error",
    "messages": ["unknown_language"],
    "errorcode": 404
}
```
**`path`的路径不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```

---

## new_proposal_create
创建一个`create tag`提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**方法**：`POST`.  
**路径**：`/api/v1/tag/proposal/create`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `name` | array[string] | 是 | - | 创建的标签的名称列表。 |
| `lang` | string | 是 | - | 创建的tag的名称的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `path` | string | 否 | `"/"` | 在指定路径下的创建标签。 |

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
**`name`为空**：
```json
{
    "status": "error",
    "messages": ["empty_name_list"],
    "errorcode": 400
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
**`path`的路径不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_path"],
    "errorcode": 404
}
```

---

## new_proposal_remove
创建一个`remove tag`提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**方法**：`POST`.  
**路径**：`/api/v1/tag/proposal/remove`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `UUID` | string | 是 | - | 移除的标签的UUID。 |

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
**`UUID`对应的标签不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_tag"],
    "errorcode": 404
}
```

---

## new_proposal_add_name
创建一个`add tag name`提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**方法**：`POST`.  
**路径**：`/api/v1/tag/proposal/name/add`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `UUID` | string | 是 | - | 添加名称的标签的UUID。 |
| `lang` | string | 是 | - | 标签的新名称的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `name` | string | 是 | - | 标签的新名称。 |

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
**`UUID`对应的标签不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_tag"],
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
**`name`在标签的名称列表中已存在**：
```json
{
    "status": "warning",
    "messages": ["name_already_existence"],
    "errorcode": 0
}
```

---

## new_proposal_delete_name
创建一个`delete tag name`提案.  
需要经过[PoW证明](../PoW/PoW.md)，难度值为`4`.  
**方法**：`POST`.  
**路径**：`/api/v1/tag/proposal/name/delete`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `UUID` | string | 是 | - | 移除名称的标签的UUID。 |
| `lang` | string | 否 | `""` | 标签移除的名称的语言（遵循ISO639-1和ISO3166，如简中为zh_CN），空表示不限定语言。 |
| `name` | string | 是 | - | 标签移除的名称。 |

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
**`UUID`对应的标签不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_tag"],
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
**`name`在标签的名称列表中不存在**：
```json
{
    "status": "warning",
    "messages": ["name_non_existence"],
    "errorcode": 0
}
```