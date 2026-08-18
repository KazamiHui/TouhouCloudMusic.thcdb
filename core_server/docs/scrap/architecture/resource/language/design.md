# 自然语种内容表

用于辅助resource的构建.

数据结构如下：
- UUID.
对应一个resource.
- 语言.
标记该行的语言.
- 表键及其内容.

该表逻辑上内嵌于`UUID`对应的resource的对应表键的表值中.