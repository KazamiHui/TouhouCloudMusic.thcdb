# user_reset_password
接收一个数组，元素为键值对，包含以下内容：
- ID.
键名为`ID`，键值为字符串.
- 新密码.
键名为`new_password`，键值为字符串.
- Answer.
键名为`A`，键值为字符串.
## 储存内容
用经过*不可逆单向加密*后的`新密码`覆盖`ID`对应的账户的`密码`.
## 错误处理
如果`ID`对应的账户不存在或`ID`对应的账户的`销户`不为`0`，则返回`unknown_account`.
如果`Answer`与`ID`对应的账户的`A`不同，则返回`answer_incorrect`.