# 苍穹外卖（sky-take-out）· 后端服务

「苍穹外卖」是一套餐饮外卖与门店管理系统的完整实现：管理端负责菜品、套餐、员工、订单和营业报表；用户端（微信小程序侧）负责浏览菜品、购物车、下单、支付与地址簿。本仓库是**后端服务**，前端管理界面见 [sky-take-out-admin-vue](https://github.com/csbslh/sky-take-out-admin-vue)。

## 技术栈

| 分类 | 技术 |
|---|---|
| 框架 | Spring Boot 2.7.3、Spring MVC、Spring AOP |
| 持久层 | MyBatis、Druid 连接池、PageHelper 分页 |
| 数据库 | MySQL |
| 缓存 | Redis（Spring Cache + StringRedisTemplate） |
| 安全 | JWT 双令牌（管理端 / 用户端两套拦截器） |
| 实时通信 | WebSocket（来单语音提醒） |
| 文件存储 | 阿里云 OSS |
| 支付 | 微信支付 API v3（下单、支付回调、退款） |
| 报表 | Apache POI 导出运营数据 Excel |
| 接口文档 | Knife4j |

## 模块结构

```
sky-take-out
├── sky-common   公共模块：常量、异常、统一返回、JWT/OSS/微信支付工具、配置属性
├── sky-pojo     实体模块：entity / dto / vo
└── sky-server   服务模块：controller（admin/user）、service、mapper、config、interceptor、task、websocket
```

分层调用链：`Controller → Service → Mapper → MySQL`，管理端与用户端接口分别位于 `controller/admin` 与 `controller/user`，由两个 JWT 拦截器分别校验身份。

## 主要功能

**管理端**

- 员工登录、员工管理
- 分类管理、菜品管理（含口味、图片上传 OSS）
- 套餐管理（套餐与菜品关联）
- 订单管理：接单/拒单、派送、完成、取消，条件分页查询
- 营业数据：营业额、订单、用户、销量 Top10 统计与 Excel 导出
- 工作台数据总览、来单语音提醒（WebSocket）

**用户端**

- 微信登录、菜品与套餐浏览
- 购物车、订单提交与支付、历史订单
- 地址簿管理
- 支付回调通知处理

## 快速开始

### 1. 初始化数据库

```bash
mysql -u root -p < sky.sql
```

### 2. 准备配置

`sky-server/src/main/resources/` 下的配置文件不入库（含密钥），请复制样例并填写：

```bash
cp application-example.yml application.yml
cp application-dev-example.yml application-dev.yml
```

需要填写的包括：MySQL 账号密码、Redis 地址与密码、阿里云 OSS 的 AccessKey、微信支付商户号与证书路径、JWT 签名密钥。**微信支付与 OSS 相关功能需自备账号，未配置时其余功能可正常运行。**

### 3. 启动

```bash
mvn clean package -DskipTests
java -jar sky-server/target/sky-server-1.0-SNAPSHOT.jar
```

或在 IDEA 中直接运行 `com.sky.SkyApplication`。服务默认端口 `8080`，接口文档见 `http://localhost:8080/doc.html`。

## 环境要求

- JDK 17（源码按 Java 17 编译）
- MySQL 8.x、Redis 6.x
- Maven 3.6+

## 说明

- 仓库不包含任何真实密钥与证书，所有敏感配置通过 `application-dev.yml` 本地填写，且已被 `.gitignore` 排除。
- 微信支付、阿里云 OSS 属于外部依赖，没有对应账号时相关接口会报错，其余业务流程不受影响。

## 作者

陈杉 · 武昌工学院 信息工程学院 软件工程
