# Sky-Take-Out 苍穹外卖系统

![GitHub License](https://img.shields.io/badge/license-Unlicense-blue) 
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-2.7.3-green)

基于Spring Boot的外卖行业全栈解决方案，支持多终端协同、微信登录、实时订单追踪与智能数据分析。项目源自黑马程序员教学视频，重构优化代码结构与功能实现。

---
## 📖 学习路径
### 🔗 核心教学资源
- [【原课】苍穹外卖B站全流程开发](https://www.bilibili.com/video/BV1TP411v7v6)  
  时长32小时，覆盖从环境搭建到支付集成的完整开发流程

## 📚 项目概览
### 核心功能
- **多角色系统**：支持商家端（订单管理/菜品分类/数据统计）、用户端（微信登录/购物车/支付）、骑手端（订单派送）
- **智能业务**  
  - 自动填充公共字段（创建时间、操作用户等）
  - Redis缓存菜品与套餐数据提升响应速度
  - Spring Task定时任务处理超时订单
  - WebSocket实现催单/来单提醒

### 技术矩阵
| 类别         | 技术栈                                                                 |
|--------------|----------------------------------------------------------------------|
| 核心框架     | Spring Boot 2.7.3、Spring MVC、MyBatis-Plus                          |
| 安全认证     | JWT令牌、ThreadLocal线程存储、AOP自动填充                            |
| 中间件       | Redis（缓存/会话）、RabbitMQ（消息队列）、Nginx（反向代理）          |
| 微信生态     | 小程序登录、微信支付、OpenID身份识别                                 |
| 数据层       | Druid连接池、PageHelper分页、POI报表导出                             |
| 工具链       | Lombok、Swagger-Knife4j、HttpClient、WebSocket                      |

---

## 🛠️ 快速部署
### 环境要求
- JDK 17+
- MySQL 8.0+
- Redis 7.0+
- Maven 3.6+

### 配置指引
1. **数据库初始化**  
   执行`/sql/sky.sql`初始化表结构与测试数据，注意修改`application-dev.yml`中的数据库连接配置

2. **微信配置**  
   在`application.yml`中配置：
   ```yaml
   sky:
     wechat:
       appid: YOUR_APPID
       secret: YOUR_SECRET
     jwt:
       user-secret-key: 自定义密钥
   ```
3. **Nginx反向代理**
   修改nginx.conf实现端口映射，注意使用UTF-8无BOM编码保存配置文件避免启动异常：
   ```nginx
   server {
       listen       80;
       server_name  localhost;
      
       location / {
           proxy_pass http://localhost:8080;
           proxy_set_header Host $host;
       }
   }
   ```
## 🌐 系统架构

### 模块化设计
| 模块名称     | 功能说明                                                                 |
|--------------|--------------------------------------------------------------------------|
| `sky-common` | 通用工具包（日期处理/异常拦截/上下文管理），含`AutoFill`注解实现字段自动填充 |
| `sky-pojo`   | 数据模型层（DTO/VO/BO），严格对应数据库表结构                             |
| `sky-server` | 业务逻辑实现层（Controller/Service/Mapper），采用三层架构设计             |

### 关键实现解析
- #### AOP自动填充
  通过`@AutoFill`注解与切面编程，统一处理20+业务表的create_time/update_time等公共字段

- #### 微信支付闭环
  采用预支付订单→签名验证→支付状态回调的全流程设计，支持沙箱环境测试

- #### 多维度统计
  提供营业额/用户增长/订单完成率/Top10商品销量等多维度数据看板
