> 阅读指南：
> 1. 建议使用 Obsidian 阅读器以获得最佳体验
> 2. 本文档凌晨赶工，若有疏忽请谅解
## 总体架构设计

![Pasted image 20250222010901.png](https://github.com/StealthIM/server-arch/blob/main/Pasted%20image%2020250222010901.png?raw=true)

---

## 模块名称（仓库）

| 模块名称                    | 中文名      | 层级  | 语言  | 是否现有 | 开发状态 | 客户端访问 | 模块开发者 |
| ----------------------- | -------- | --- | --- | ---- | ---- | ----- | ----- |
| server-master           | 中控       | M   | cs  |     | IG🟨   |       | @Elipese568  |
| server-db_gateway       | 数据库网关    | G   | go  |     | IT🟪   |       | @cxykevin   |
| server-status_watchdog  | 状态监视     | A   |     |      | TD🟥   | ✅     |       |
| server-service_gateway  | 业务网关     | A   | cf  | ✅    | TD🟥   | ✅     |       |
| server-mysql            | MySQL    | D   | cf  | ✅    | TD🟥   |       |       |
| server-redis            | Redis    | D   | cf  | ✅    | TD🟥   |       |       |
| server-rocketmq         | RocketMQ | D   | cf  | ✅    | TD🟥   |       |       |
| server-service_system   | 集成业务系统   | S   |     |      | TD🟥   |       |       |
| server-user_system      | 用户管理系统   | S   |     |      | ID🟧   |       | @Alex-omega      |
| server-file_api_manager | 文件API管理器 | W   | go  |      | ID🟧   |       | @cxykevin      |
| server-file_manager     | 文件资源管理器  | F   | py  |      | IG🟨  |       | @cxykevin      |
| server-message_manager  | 消息管理器    | W   | go  |      | ID🟧   |       | @Keniis0712      |
| server-group_manager    | 群组管理器    | W   | go  |      | ID🟧   |       | @Keniis0712      |
| server-ldg_manager      | 用户管理器    | W   |     |      | ID🟧  |       | @Elipese568     |
| server-access_manager   | 鉴权管理器    | W   |     |      | TD🟥   |       |       |
> 注：
> - 空格表明未定
> - 层级缩写说明：
> 	- M: Master（中控）
> 	- G: Gateway（网关）
> 	- A: API provider for client（面向客户端的API）
> 	- D: Database（数据库）
> 	- S: System of APIs（API集成模块）
> 	- W: Worker（业务API实际存在的地方）
> 	- F: File manager（文件交互）
> - 语言缩写说明：
> 	- py: Python
> 	- cs: C#
> 	- js: Java Script
>   - go: GoLang
> 	- cf: configure（依赖已有软件，仅编写配置）
> - 开发状态说明：
> 	- TD🟥: Todo（未开始）
> 	- ID🟧: In Design（设计中）
> 	- IG🟨: In Progress（编写中）
> 	- IT🟪: In Testing（测试中）
> 	- CD🟦: Completely Done（已完成）
> 	- PB🟩: Published（已上线）
> - 其他说明：
> 	1. TD状态的模块可自由认领，认领后修改状态为WD【此后按需修改】并修改开发者
> 	2. 除TD、WD外其他状态的模块可跟初始开发者确认后参与，并在开发者栏补充

`Service`中每一层仅向下一层发送请求或调用函数，例如`server-file_api_manager`提供访问文件等操作的API，而其能操作的仅有数据库和`server-file_manager`中的API。

最终所有面向`client`的API全部通过`server-service_system`和`server-user_system`进行集中访问，而客户端向且仅向`server-service_gateway`发送请求，业务网关根据`url`转发到两个`system`。
