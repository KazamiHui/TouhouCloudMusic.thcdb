# user_signup
## 输入
接收以下内容：
- 所在的group的UID
键名为`group_id`.
- ID.
键名为`id`，键值为字符串.
- 密码.
键名为`password`，键值为字符串.
- Question.
键名为`Q`，键值为字符串.
- Answer.
键名为`A`，键值为字符串.
## 参数规范
`ID`在每个group下必须唯一.
## 日志记录
执行结束记录INFO等级日志，执行情况为`success, new user with {ID}`.
## 储存内容
`id`储存进`ID`.
`密码`经过*不可逆单向加密*后储存进`密码`.
`comment,gain_permission,resource_rectify,create_personal_list`储存进`主权限`.
`Question`储存进`Q`.
`Answer`经过*不可逆单向加密*后储存进`A`.
## 错误处理
如果`ID`在表中不唯一，则返回`ID_not_unique`.
如果`密码`为空，则返回`empty_password`.
如果`Question`或`Answer`中任一为空，则返回`without_Q_or_A`.