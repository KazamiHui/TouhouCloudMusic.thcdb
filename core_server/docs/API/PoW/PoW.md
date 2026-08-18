# PoW
哈希函数为`SHA-256`，难度值表示与基准值的差，难度值与基准值的差为十六进制前导零字符数，目前基准值为`0`.  
挑战值为UTF-8编码的字符串.  
挑战值结构为生成时的unix秒级时间戳用分隔符`:`连接随机字符串（仅英文字母和数字，长度256），如`1704392701:ascc5861faxxczx...`（`...`表示省略不写）.  
挑战值有效时间为3min（从挑战值的时间戳开始算起）.

**方法**：`GET`.  
**路径**：`/api/v1/PoW/{难度值}`.

## 成功响应（HTTP200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0,
    "challenge": "挑战值"
}
```

## 错误响应
**请求的难度值大于10或不为正数**：
```json
{
    "status": "error",
    "messages": ["unknown_difficulty"],
    "errorcode": 403
}
```

## 关于被校验的接口
难度值以各接口文档描述为准.  
需要校验的接口及其难度如下：
- 难度值为`4`：
    - `/api/v1/resource/proposal/new/modify`.
    - `/api/v1/resource/proposal/new/create`.
    - `/api/v1/resource/proposal/new/delete`.
    - `/api/v1/resource/proposal/new/revoke`.
    - `/api/v1/resource/proposal/new/merge`.
    - `/api/v1/tag/proposal/create`.
    - `/api/v1/tag/proposal/remove`.
    - `/api/v1/tag/proposal/name/add`.
    - `/api/v1/tag/proposal/name/remove`.
    - `/api/v1/music_collection/create`.
    - `/api/v1/examine/affair/comment/create`.
    - `/api/v1/examine/affair/comment/remove`.
    - `/api/v1/examine/affair/comment/report`.
    - `/api/v1/examine/appeal/create`.
- 难度值为`6`：
    - `/api/v1/user/signup`.
    - `/api/v1/examine/affair/approve`.
    - `/api/v1/examine/affair/reject`.
- 难度值为`10`：
    - `/api/v1/org/authority/grant`.
    - `/api/v1/org/authority/strip`.
    - `/api/v1/org/authority/blacklist/add`.
    - `/api/v1/org/authority/blacklist/remove`.
    - `/api/v1/org/user/permission/grant/gain_auth`.
    - `/api/v1/org/user/permission/strip/gain_auth`.

### 请求体内容
| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| `PoW_challenge` | string | 是 | - | 用于校验的挑战值。 |
| `PoW_nonce` | string | 是 | - | 满足难度的nonce，UTF-8编码。 |

### 含有非法参数的响应
```json
{
    "status": "error",
    "messages": ["illegal_value", ["非法参数名"]],
    "errorcode": 400
}
```
### 挑战值失效响应
通过`PoW_challenge`的时间戳判断.
```json
{
    "status": "error",
    "messages": ["challenge_timeout"],
    "errorcode": 400
}
```
### 校验失败响应
`PoW_challenge`和`PoW_nonce`两个字符串按`PoW_challenge + PoW_nonce`的顺序直接拼接后，计算其哈希值是否符合需要校验的接口本身要求的难度来校验.
```json
{
    "status": "error",
    "messages": ["error_nonce"],
    "errorcode": 422
}
```