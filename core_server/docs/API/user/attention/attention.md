# 关注

这里的关注是对于resource的一些字段，和like的翻译得到的“关注”语义不同.

---
## 目录
- [create](#create)
- [list](#list)
- [change](#change)
- [remove](#remove)
---

## create
创建一个关注.  
**方法**：`POST`.  
**路径**：`/api/v1/attention/create`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | string | 是 | - | resource的UUID。 |
| `info` | array[string] | 是 | - | 关注的信息，可选项见[各类resource的字段](../about_resource.md#各类resource的字段)。 |

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
**关于请求的resource的关注已存在**：
```json
{
    "status": "error",
    "messages": ["attention_already_existence"],
    "errorcode": 403
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
**请求的resource的类型不存在`info`中的字段**：
```json
{
    "status": "error",
    "messages": ["unknown_key", ["不存在的字段"]],
    "errorcode": 422
}
```
**未提供关注的字段**：
```json
{
    "status": "error",
    "messages": ["empty_attention"],
    "errorcode": 400
}
```

---

## list
获取关注列表.  
**方法**：`GET`.  
**路径**：`/api/v1/attention/list`.

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
关注字段数据的消息体为`json`，格式如下：
```json
{
    "UUID": "resource的UUID",
    "info": ["关注的字段"]
}
```

---

## change
修改一个关注的信息.  
**方法**：`PATCH`.  
**路径**：`/api/v1/attention/change`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | string | 是 | - | resource的UUID。 |
| `info` | object | 是 | - | 关注的信息，键名为`add`或`remove`，键值的可选项见[各类resource的字段](../about_resource.md#各类resource的字段)。 |

如果修改后的关注为空，则删除这个关注.

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
**请求的resource不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```
**请求的resource的类型不存在`info`中的字段**：
```json
{
    "status": "error",
    "messages": ["unknown_key", ["不存在的键"]],
    "errorcode": 422
}
```
**修改的关注不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_attention"],
    "errorcode": 404
}
```
**修改中移除的关注信息不存在**：
```json
{
    "status": "warning",
    "messages": ["info_non_existence"],
    "errorcode": 0
}
```

---

## remove
删除一个对某个resource的关注.  
**方法**：`PUT`.  
**路径**：`/api/v1/attention/remove`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `UUID` | string | 是 | - | resource的UUID。 |

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
**请求的resource不存在**：
```json
{
    "status": "error",
    "messages": ["unknown_resource"],
    "errorcode": 404
}
```
**删除的关注不存在**：
```json
{
    "status": "warning",
    "messages": ["attention_non_existence"],
    "errorcode": 0
}
```