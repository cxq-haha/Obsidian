命名规范：

| 含义描述                                                  | 命名规范示例  |
| --------------------------------------------------------- | ------------- |
| post body请求参数(前端提交过来的请求对象)                 | XxRequest     |
| 表示层对象命名(返回给前端展示的对象)                      | XxVo          |
| 数据传输对象命名                                          | XxDto         |
| es实体名命名                                              | XxIndexDO     |
| db实体名命令                                              | 跟表名相同    |
| monogo实体命名                                            | XxDoc         |
| db组合关联实体命名                                        | Xx            |
| Service接口命名                                           | XxService     |
| service实现命名                                           | XxServiceImpl |
| manager,service引入多个Manager进行负责的组合业务逻辑      | XxManager     |
| dao层命名                                                 | XxMapper      |
| 封装持久化组合服务（一个实体需要从db,ex,redis多种存储获取 | XxRepository  |


Po --> Dto --> Vo

对象转换工具：mapstruct

![[Pasted image 20221225203927.png]]


