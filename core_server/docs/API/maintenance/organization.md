## 登记
登记一个组织.  
**方法**：`POST`.  
**路径**：`/api/maintenance/org/register`.

### 请求体
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `PoW_nonce` | string | 是 | - | PoW部分，见[PoW接口文档](../PoW/PoW.md#需要校验接口的描述)。 |
| `id` | string | 是 | - | 创建的组织ID。 |
| `leader` | object | 是 | - | 总管理的账户信息。 |
| `resource_manager` | object | 是 | - | resource总管的账户信息。 |
| `community_manager` | object | 是 | - | 社区总管的账户信息。 |

上表三个账户的信息必须包含下表字段，且他们默认归属创建的组织.
| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `profile_id` | string | 注册的账户的ID。 |
| `password` | string | 注册的账户的密码。 |
| `Q_and_A` | object | 用于后续解冻、冻结、注销、恢复等操作。键名为题目，键值为答案。至少一组。 |

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
**组织ID已被占用**：
```json
{
    "status": "error",
    "messages": ["repeated_ID"],
    "errorcode": 409
}
```
**账户缺少字段**：
```json
{
    "status": "error",
    "messages": ["missing_key", ["缺少的字段名"]],
    "errorcode": 422
}
```
**三个账户之间存在重复ID**：
```json
{
    "status": "error",
    "messages": ["repeated_ID", ["user"]],
    "errorcode": 409
}
```