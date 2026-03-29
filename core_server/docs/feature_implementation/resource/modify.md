# resource_modify
接收以下内容：
- UUID.
键名为`UUID`，键值为字符串.
- 修改内容.
键名为`modify_data`，键值为一些键值对.
### 参数规范
`UUID`必须为已有的resource对象的UUID.
`modify_data`的键值的键名必须为`UUID`对应的对象含有的键.
### 日志记录
执行结束记录INFO等级日志，执行情况为`success, modify {UUID} fields: {键名列表}`.
### 错误处理
- 如果`UUID`没有对应的resource对象，则返回`unknown_resource`.
- 如果`modify_data`中的数组元素的键名存在`UUID`对应resource对象的类型没有的键，则返回`unknown_key`.