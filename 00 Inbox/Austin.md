## 使用的技术栈
![[Pasted image 20220828181941.png]]

## 各模块的功能
| 模块    | 作用                                  |
| ------- | ------------------------------------- |
| common  | 基本信息->POJO/枚举配置               |
| API     | 服务接口                              |
| APIImpl | 服务接口实现实现（考虑以后会介入RPC） |
| support | Data获取->DB/Redis/Elasticsearch      |
| stream  | Fink的实时清洗                        |
| cron    | 定时任务逻辑                          |
| handler | 逻辑层                                |
| web     | 后台管理和接口                        |

前端项目： https://gitee.com/zhongfucheng/austin-admin

## 项目架构图
![[Pasted image 20220830210218.png]]
### 各个模块的作用
austin-admin：前端模块，通过它实现对管理消息以及查看消息下发情况

austin-web：提供接口给austin-admin调用

austin-cron：定时任务处理工作

MQ：发送消息实际上是调用各个服务提供的API，假设某消息的服务超时，`austin-api`如果是直接调用服务，那存在**超时**风险，拖垮整个接口性能。MQ在这是为了做异步和解耦，并且在一定程度上抗住业务流量。

austin-handler：做一些通用业务处理和发送消息

austin-stream：`austin-handler`会产生大量的日志数据。日志数据会被收集至MQ，由`austin-stream`流式处理模块进行消费并最后将数据写入至`austin-datahouse`

