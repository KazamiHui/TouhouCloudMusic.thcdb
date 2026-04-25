# user_close
接收一个数组，元素为键值对，包含以下内容：
- ID.
键名为`ID`，键值为字符串.
- 密码.
键名为`password`，键值为字符串.
- Answer.
键名为`A`，键值为字符串.
#### 储存内容
将`ID`对应账户的`销户`的设为请求销户的10位UNIX时间戳.
#### 错误处理
如果`ID`对应的账户不存在，则返回`unknown_account`.
如果`密码`与`ID`对应的账户的`密码`不同，则返回`password_incorrect`.
如果`Answer`与`ID`对应的账户的`A`不同，则返回`answer_incorrect`.
如果`ID`对应的账户的`销户`不为`0`，则返回`already_deleted`.