# examine_and_verify_approve
接收两个键值对，内容如下：
- 提案UID.
键名为`proposalUID`，键值为通过的提案的`UID`.
- 审批人.
键名为`approver`，键值为通过的人的`UID`.
### 参数规范
`提案UID`必须在提案表中有对应的`提案`.
`审批人`必须在user表中有对应的`user`.
### 工作流程
1. 对`提案UID`对应的`提案`上读写锁，不对整张表上锁.
2. 执行`提案UID`对应的`提案`中的`请求`的`请求类型`调用函数，并传入`请求内容`.
3. 结合版本控制系统，产生一个history.
4. 删除`提案UID`对应的`提案`.
### 错误处理
如果`提案UID`不存在对应的`提案`，则返回`unknown_proposal`.
如果`审批人`在user表中不存在对应的`user`，则返回`unknown_account`.