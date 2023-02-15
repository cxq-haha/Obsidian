尚硅谷Sentinel学习资料

[[Sentinel核心源码解析-课堂笔记.pdf]]


[[Sentinel源码+demo.zip]]

官方文档：

https://sentinelguard.io/zh-cn/docs/introduction.html

围绕资源状态设置规则：
- 流量控制规则
- 熔断降级规则
- 系统保护规则

![[Pasted image 20221210151457.png]]


系统自适应限流：
机器负载、CPU 使用率，总体平均响应时间、入口 QPS 和并发线程数

Sentinel 中的系统自适应其实指的是按照应用所在机器的操作系统负载，再结合应用本身的请求处理能力进行的自适应

