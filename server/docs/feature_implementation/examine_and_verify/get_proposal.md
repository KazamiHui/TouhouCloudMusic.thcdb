# examine_and_verify_get_proposal
接收一个UID.
返回`UID`对应的提案的除`UID`以外的字段.
### 参数规范
`UID`必须在`提案表`中存在.
### 错误处理
如果`UID`不存在对应的`提案`，则返回`unknown_proposal`.