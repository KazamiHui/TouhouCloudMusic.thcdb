# resource_delete
接收一个`UUID`.
### 参数规范
`UUID`必须为已有的resource对象的UUID.
### 日志记录
执行结束记录INFO等级日志，执行情况为`success, delete {UUID}`.
### 错误处理
如果`UUID`没有对应的resource对象，则返回`unknown_resource`.