# user_signin
接收一个键值对，键名为`ID`，键值为`密码`.
#### 返回值
返回一串二进制编码作为登录凭证，有效期24小时.
#### 错误处理
如果`ID`对应的账户不存在，则返回`unknown_account`.
如果`ID`对应的账户的`销户`不为`0`，则返回`was_delete_account`.
如果`密码`与`ID`对应的账户的`密码`不同，则返回`password_incorrect`.