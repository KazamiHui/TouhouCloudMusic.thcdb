# resource_search
接收两个字符串和一个数组，内容如下：
- 关键词.
类型为字符串.
- 过滤.
类型为数组.
- 排序.
类型为字符串.
## 错误处理
如果有非法参数，则返回`illegal_value`，日志记录Warning等级日志，动作执行情况为`{参数名}:{参数值} is illegal value!`.
如果`关键词`为空，且推荐系统未实现，则返回`search_unhandled_request`.