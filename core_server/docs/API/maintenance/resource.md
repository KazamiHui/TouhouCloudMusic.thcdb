# maintenance_resource

都需要给定语言.

---
## 目录
- [create](#create)
- [delete](#delete)
- [modify](#modify)
- [merge](#merge)
- [revoke](#revoke)
---

## create
该操作不对用户开放，用于项目组的最终修正手段.  
**权限要求：admin**.  
**方法**：`PUT`.  
**路径**：`/api/maintenance/resource/create`.

### 请求体
请求体使用`multipart/related`包装，其中首个为json，内容如下：
```json
{
    "type": "创建的resource的类型",
    "any": {
        "key1": "key1的索引"
    },
    "language1": {
        "key2": "key1的索引",
        "key3": "key2的索引"
    }
}
```
此处`key1`、`language1`为占位.  
索引使用Content-ID，格式请自行确定.  
不区分语言的键在`any`字段内，多语言的键都必须包裹在某个`language`字段的内，请翻阅[about_resource](./about_resource.md#各类resource的结构)确定.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**无权限进行**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数的索引"]],
    "errorcode": 400
}
```
**不存在的语言**：
```json
{
    "status": "error",
    "messages": ["unknown_language"],
    "errorcode": 422
}
```
**不存在的键**：
```json
{
    "status": "error",
    "messages": ["unknown_key"],
    "errorcode": 422
}
```
**不存在的类型**：
```json
{
    "status": "error",
    "messages": ["unknown_resource_type"],
    "errorcode": 422
}
```

---

## delete
该操作不对用户开放，用于项目组的最终修正手段.  
**权限要求：admin**.  
**方法**：`PUT`.  
**路径**：`/api/maintenance/resource/delete`.

### 请求体
json字符串，内容即为要删除的对象的UUID，如`"550e8400-e29b-41d4-a716-446655440000"`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**无权限进行**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**对象不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```
**非法数据类型**：
```json
{
    "status": "error",
    "messages": ["illegal_value"],
    "errorcode": 400
}
```

---

## modify
该操作不对用户开放，用于项目组的最终修正手段.  
**权限要求：admin**.  
**方法**：`PATCH`.  
**路径**：`/api/maintenance/resource/modify`.

### 请求体
请求体使用`multipart/related`包装，其中首个为json，下表为该json的描述.

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | string | 是 | - | 修改的resource的UUID。 |
| `data` | object | 是 | - | 修改的内容，不传的键保持原值，结构见后文。 |

`data`字段结构如下：
```json
{
    "any": {
        "key1": "key1的索引"
    },
    "language1": {
        "key2": "key1的索引",
        "key3": "key2的索引"
    }
}
```
此处`key1`、`language1`为占位.  
这`language`和`any`字段名仅作为语言标识符，如果无内容可不写.  
索引使用Content-ID，格式请自行确定.  
不区分语言的键在`any`字段内，多语言的键都必须包裹在某个`language`字段的内，请翻阅[about_resource](./about_resource.md#各类resource的结构)确定.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**无权限进行**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**对象不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```
**含有非法参数**：
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数的索引"]],
    "errorcode": 400
}
```
**不存在的语言**：
```json
{
    "status": "error",
    "messages": ["unknown_language"],
    "errorcode": 422
}
```
**不存在的键**：
```json
{
    "status": "error",
    "messages": ["unknown_key"],
    "errorcode": 422
}
```

---

## merge
该操作不对用户开放，用于项目组的最终修正手段.  
**权限要求：admin**.  
**方法**：`PUT`.  
**路径**：`/api/maintenance/resource/merge`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | array[string] | 是 | - | 固定长度为2的json数组，数组元素为要合并的两个对象的UUID，如`"550e8400-e29b-41d4-a716-446655440000"`. |
| `keep` | object | 是 | - | 对于具有唯一值的字段指定保留谁的值，`first`表示第一个，`second`表示第二个 |

示例：
```json
{
    "UUID": ["550e8400-e29b-41d4-a716-446655440000", "782e8217-e28b-41d6-a817-436657740220"],
    "keep": {
        "key1": "first",
        "key2": "second"
    }
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
**无权限进行**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**对象不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource", ["不存在的resource的UUID"]],
    "errorcode": 404
}
```
**两个对象类型不同**：
```json
{
    "status": "error",
    "messages": ["different_resource_type"],
    "errorcode": 422
}
```
**键不存在于对象类型中**：
```json
{
    "status": "error",
    "messages": ["unknown_key",["未知的键的名"]],
    "errorcode": 404
}
```
**未指定具有唯一值的键的保留对象**：
```json
{
    "status": "error",
    "messages": ["unknown_key_value"],
    "errorcode": 422
}
```
**指定了不具有唯一值的键的保留对象**（合并正常进行）：
```json
{
    "status": "warning",
    "messages": ["key_not_unique_value", ["不具有唯一键值的键名"]],
    "errorcode": 0
}
```

---

## revoke
该操作不对用户开放，用于项目组的最终修正手段.  
**权限要求：admin**.  
**方法**：`PUT`.  
**路径**：`/api/maintenance/resource/revoke`.

### 请求体
json字符串，内容即为要回退版本的对象的UUID，如`"550e8400-e29b-41d4-a716-446655440000"`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

### 错误响应
**无权限进行**：
```json
{
    "status": "error",
    "messages": ["no_permission"],
    "errorcode": 403
}
```
**对象不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```
**非法数据类型**：
```json
{
    "status": "error",
    "messages": ["illegal_value"],
    "errorcode": 400
}
```