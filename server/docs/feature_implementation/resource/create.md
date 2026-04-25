# resource_create
接收以下内容：
- 类型.
键名为`type`，键值为resource中的任一类型.
- 数据.
键名为`data`，键值类型为数组，数组元素类型为键值对.
### 参数规范
`type`必须为已有的resource类型.
`data`的键值的元素，键值对的键名必须为`type`类型resource的键.
### 日志记录
执行结束记录INFO等级日志，执行情况为`success, create {type} resource with {UUID}`.
### 错误处理
- 如果`type`不是resource类型中的一种，则返回`unknown_type`.
- 如果`data`中的数组元素的键名存在`type`对应resource类型没有的键，则返回`unknown_key`.