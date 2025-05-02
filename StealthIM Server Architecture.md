## 总体架构设计

![arch image](https://raw.githubusercontent.com/StealthIM/server-arch/refs/heads/main/arch.svg)

---

## 通信协议

模块间通信协议使用 GRPC，定义 `.proto` 文件位于 `server_protos` 仓库下与每个子仓库下的副本

## 模块名称（仓库）

| 模块名称                    | 中文名      | 层级  | 语言  | 是否现有 | 开发状态 | 客户端访问 | 模块开发者 | 备注 | 仓库 |
| ----------------------- | -------- | --- | --- | ---- | ---- | ----- | ----- | - | - |
| server-master           | 中控       | M   | cs  |     | ID🟧   |       |  | |  |
| server-db_gateway       | 数据库网关    | G   | go  |     | CD🟦   |       | @cxykevin | | [StealthIMDB](https://github.com/StealthIM/StealthIMDB) |
| server-status_watchdog  | 状态监视     | A   |     |      | TD🟥   | ✅     | @Keniis0712 | | |
| server-service_gateway  | 业务网关     | A   | cf  | ✅    | TD🟥   | ✅     |  | | |
| server-mysql            | MySQL    | D   | cf  | ✅    | TD🟥   |       |       | | |
| server-redis            | Redis    | D   | cf  | ✅    | TD🟥   |       |       | | |
| server-rocketmq         | RocketMQ | D   | cf  | ✅    | TD🟥   |       |       | 性能原因暂时保留不使用 | |
| server-service_system   | 集成业务系统   | S   |     |      | TD🟥   |       |       | | |
| server-user_system      | 用户管理系统   | S   |     |      | ID🟧   |       | @Alex-omega      | | |
| server-file_api_manager | 文件API管理器 | W   | go  |      | IT🟪   |       | @cxykevin      | | [StealthIMFileAPI](https://github.com/StealthIM/StealthIMFileAPI) |
| server-file_manager     | 文件资源管理器  | F   | py  |      | CD🟦  |       | @cxykevin      | | [StealthIMFileStorage](https://github.com/StealthIM/StealthIMFileStorage) |
| server-message_manager  | 消息管理器    | W   | go  |      | TD🟥   |       |  | | |
| server-group_manager    | 群组管理器    | W   | go  |      | TD🟥   |       |  | | |
| server-session_manager  | 会话管理器    | W   | go  |      | IT🟪  |       | @cxykevin     | | [StealthIMSession](https://github.com/StealthIM/Session) |
| server-ldg_manager      | 用户管理器    | W   | go  |      | ID🟧  |       | @cxykevin | | |
| server-access_manager   | 鉴权管理器    | W   |     |      | TD🟥   |       |       | | |
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
> 	- js: JavaScript
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
> 	1. TD状态的模块可自由认领，认领后修改状态为ID【此后按需修改】并修改开发者
> 	2. 除TD、ID外其他状态的模块可跟初始开发者确认后参与，并在开发者栏补充

`Service`中每一层仅向下一层发送请求或调用函数，例如`server-file_api_manager`提供访问文件等操作的API，而其能操作的仅有数据库和`server-file_manager`中的API。

最终所有面向`client`的API全部通过`server-service_system`和`server-user_system`进行集中访问，而客户端向且仅向`server-service_gateway`发送请求，业务网关根据`url`转发到两个`system`。
