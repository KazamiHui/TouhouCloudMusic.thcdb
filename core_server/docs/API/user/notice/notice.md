# 通知

---
## 目录
- [connect_notice](#connect_notice)
- [disconnect_notice](#disconnect_notice)
- [notice_list](#notice_list)
---

## connect_notice
连接到通知系统，持续推送新通知.  
**方法**：`POST`.  
**路径**：`/api/v1/notice/connect`.

### 成功响应 （HTTP 200）
使用SSE连接，event为`notice`或`temp_notice`（该类型的内容不存储到通知列表中），id为通知ID，data的格式如下：
```json
{
    "sender": {
        "org_id": "发送者的组织ID",
        "profile_id": "发送者的账户ID"
    },
    "receiver": {
        "org_id": "接收者的组织ID",
        "profile_id": "接收者的账户ID"
    },
    "time": "通知生成时的UNIX时间戳",
    "content": "通知内容"
}
```
重连后提供通知ID，从该通知的时间戳后继续推送通知.

---

## disconnect_notice
断开与通知系统的连接.  
**方法**：`POST`.  
**路径**：`/api/v1/notice/disconnect`.

### 成功响应 （HTTP 200）
```json
{
    "status": "success",
    "messages": [],
    "errorcode": 0
}
```

---

## notice_list
获取通知列表.  
**方法**：`GET`.  
**路径**：`/api/v1/notice/list?start={起始时间}&end={末尾时间}`.

取在`start`和`end`时间范围内的通知，时间点使用UNIX时间戳.

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
通知数据的消息体为`json`，格式如下：
```json
{
    "ID": "通知ID",
    "sender": {
        "org_id": "发送者的组织ID",
        "profile_id": "发送者的账户ID"
    },
    "time": "通知生成时的UNIX时间戳",
    "content": "通知内容"
}
```