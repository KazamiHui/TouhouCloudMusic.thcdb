# resource

除search外均需要给定语言.
resource结构见[各类resource的结构](./about_resource.md#各类resource的结构).

---
## 目录
- [search](#search)
- [data](#data)
- [new_proposal](#new_proposal)
- [create](#create)
- [delete](#delete)
- [modify](#modify)
- [merge](#merge)
- [revoke](#revoke)
---

## search
对resource按指定的条件进行检索.
**方法**：`POST`.
**路径**：`/api/resource/search`.
### 请求体（json）
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `maxsize` | integer | 否 | 5 | 每次最大返回条数，1-20。 |
| `keyword` | string | 是 | - | 搜索关键词。 |
| `resource_types` | array[string] | 是 | - | 限定类型。 |
| `filter` | object | 否 | {} | 过滤条件（可用项见[可用于filter的key](./about_resource.md#可用于filter的key)）。 |
| `sort` | string | 否 | "personal_recommend:desc" | 排序，格式 `"field:order"`（可用项见[可用于sort的key](./about_resource.md#可用于sort的key)）。 |
| `UUID_output` | boolean | 否 | false | `true`返回UUID，`false`返回resource简要信息。 |
### 响应
#### 成功响应 （HTTP 200）
如果`UUID_output`为`false`，
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "data": [
        {...}, {...}
    ]
}
```
`data`项只有对象的名称、简介（如果存在）和tag（如果存在）.

如果`UUID_output`为`true`，
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "data": [
        "uuid1", "uuid2"
    ]
}
```

#### 错误响应
**含有非法参数**（maxsize超过范围也是非法参数）：
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
**`resource_types`为空**：
```json
{
    "status": "error",
    "messages": ["unrestricted_type"],
    "errorcode": 400
}
```
**`filter`的元素不是`resource_types`对应的resource类型中的任何一个键**：
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
**路径**：`/api/resource/data`.
### 请求体（json）
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | string | 是 | - | 需要的resource的UUID。 |
| `key` | array[string] | 否 | ["name"] | 需要的resource的键的信息（可选项见[各类resource的结构](./about_resource.md#各类resource的结构)）。 |
| `language` | string | 是 | - | 返回的对象信息的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `output_data_type` | array[string] | 否 | ["text"] | 使用单数形式，现支持text和image。 |
### 响应
#### 成功响应 （HTTP 200）
响应体使用`multipart/related`封装，其中首个为json，内容如下：
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "data": {
        "texts": {
            "key1": 1,
            "key2": 2
        },
        "images": {
            "key1": 3,
            "key2": 4
        }
    }
}
```
data的项由`output_data_type`决定，key的值为该key对应的资源的Content-ID（从1开始，不含该json在内，字符串形式）.

#### 错误响应
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
**`UUID`对应的resource不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
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
**`output_data_type`有不被支持的类型**：
```json
{
    "status": "error",
    "messages": ["unknown_data_type", ["不被支持的类型"]],
    "errorcode": 400
}
```
**`UUID`对应的resource的类型不存在`key`对应的键**：
```json
{
    "status": "error",
    "messages": ["unknown_key", ["不存在的键"]],
    "errorcode": 422
}
```

---

## new_proposal
创建一个关于某个resource及其关联的resource的提案.
提案包含对resource的操作，操作类型（及其提案包含该操作需要的权限）有以下：
- create.
**resource_manager**.
- delete.
**resource_manager**.
- revoke.
**resource_manager**.
- merge.
**resource_rectify**.
- modify.
**resource_rectify**.

**方法**：`PUT`.
**路径**：`/api/resource/new_proposal`.

### 请求体
请求体使用`multipart/related`包装，其中首个为json，下表为该json的描述.

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `intro` | string | 是 | - | 提案的描述。 |
| `language` | string | 是 | - | 提案的语言（遵循ISO639-1和ISO3166，如简中为zh_CN）。 |
| `operations_data` | array[object] | 是 | - | 结构见后文。 |

`operations`引用资源时使用Content-ID字符串，格式为`"key名-数组索引"`，如`"key1-0"`表示索引为`0`的操作中名为`key1`的字段。
`operations_data`中各种操作类型的结构：
- create.
同一提案中只允许出现一次.
同一提案中后续需要`UUID`的部分可用`new-resource`占位表示使用该resource.
```json
{
    "resource_type":"创建的resource的类型",
    "operation_type":"create",
    "operation":{
        "key1":"key1的索引",
        "key2":"key2的索引"
    }
}
```
key的值为该key的资源的Content-ID（见前文）.
- delete.
同一提案中只允许出现一次.
同一提案后续不允许出现该操作的UUID.
```json
{
    "UUID":　"UUID1",
    "operation_type":"delete",
}
```
- revoke.
```json
{
    "UUID":　"UUID1",
    "operation_type":"revoke"
}
```
- merge，`operation.UUID`为被合并的resource的UUID.
**该操作会删除被合并的resource**.
所有只有唯一的值的字段必须通过`operation.keep`指定保留哪个resource的值，`oneself`占位表示`UUID`对应的resource，`merged_resource`占位表示`operation.UUID`对应的resource.
同一提案后续不允许出现被合并的resource的UUID.
```json
{
    "UUID": "UUID1",
    "operation_type":"merge",
    "operation": {
        "UUID": "UUID2",
        "keep": {
            "key1": "merged_resource",
            "key2": "oneself"
        }
    }
}
```
- modify.
```json
{
    "UUID":　"UUID1",
    "resource_type":"修改的resource的类型",
    "operation_type":"modify",
    "operation":{
        "key1":"key1的索引",
        "key2":"key2的索引"
    }
}
```
key的值为该key的资源的Content-ID（见前文）.

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
**操作的对象不存在**：
```json
{
    "status": "warning",
    "messages": ["unknown_resource",["不存在的resource的UUID"]],
    "errorcode": 404
}
```
**操作顺序非法**（如删除resource后的操作又使用了该resource）：
```json
{
    "status": "error",
    "messages": ["illegal_operation_order"],
    "errorcode": 422
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
**存在非法操作**（如modify的键值不存在）：
```json
{
    "status": "error",
    "messages": ["illegal_operation", [
        {
            "index": 非法操作的数组索引,
            "filed": "非法的参数"
        }
    ]],
    "errorcode": 422
}
```

---

## create
该操作不对用户开放，用于项目组的最终修正手段.
权限要求：**admin**.

---

## delete
该操作不对用户开放，用于项目组的最终修正手段.
权限要求：**admin**.

---

## modify
该操作不对用户开放，用于项目组的最终修正手段.
权限要求：**admin**.

---

## merge
该操作不对用户开放，用于项目组的最终修正手段.
权限要求：**admin**.

---

## revoke
该操作不对用户开放，用于项目组的最终修正手段.
权限要求：**admin**.