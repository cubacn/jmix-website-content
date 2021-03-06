## Jmix 中的数据库生命周期

### 简述

Jmix 是一个创建以数据库为中心的应用程序的框架。因此，数据模型和数据库访问层在 Jmix 框架中扮演重要角色。 在 Jmix 中我们使用关系型数据库和 JPA 来进行数据访问。

JPA 使我们摆脱了编写各种 DB 查询语句的麻烦，转而使用类和对象。这时，你需要确保数据库架构和对象模型保持状态一致，否则应用程序可能会不能启动，甚至在运行时抛出异常。 

那么，在 Jmix 我们做了哪些工作来避免这个问题，使开发人员的生活更简单一些？


### 关于数据模型的理论

无论使用何种技术，当我们要开始一个新项目并使用 ORM 框架（JPA 、iBatis 等）时，我们应该决定数据库访问层的实现模式：数据库架构和对象模型哪个优先？在 Jmix 中，我们建议从定义对象模型开始。原因很简单：模型应该定义在要使用的地方。由于所有的业务逻辑都在应用程序中处理，所以在应用程序端定义对象模型看起来很合理。

应用程序不是静态的，业务需求会一直改变，因此，应用程序的数据模型也需要改变。这意味着，底层数据库架构也会跟着改变。

由开发人员来为应用程序编写或更新 SQL 脚本的时代已经过去。现在，我们通常使用专门的数据库版本管理工具。Liquibase 和 Flyway 是最常见的两个工具，在 Jmix 中我们使用 Liquibase 来实现数据库版本化支持。

### 什么是 Liquibase?

Liquibase 的官方定义是：“Liquibase 是一个开源数据库架构变更管理解决方案，可让让您对数据库变更进行版本化管理”

核心概念是 "变更集（changeset）" - 这是 Liquibase 执行数据库更新的单位，它将各种数据库变更类型分组组织在一起，常用的数据库变更类型有创建表、移除列、更新约束等。

变更集使用 DB 无关的脚本存储在 XML、YAML 或 JSON 格式的文件中，即变更记录（changelogs）。你也可以编写特定于 DB 的变更记录，比如为 PostgreSQL 创建包含 JSONB 类型列的表，而其它数据库中这个列的类型创建为 BLOB。 

通常，在应用程序代码库中会有一个 “master” 变更记录， 在 “master” 变更记录中包含了引入其它变更记录的指令。每次创建了新的变更记录后，都会引入到  “master” 变更记录中。

Liquibase 提供了一个稳健的框架来运行数据库更新脚本。这个工具甚至可以基于已有数据库或比较两个数据库架构来创建变更记录。如果使用了 Hibernate，我们甚至可以为实体模型生成变更记录，但是需要一个单独的 Maven 插件。

### Jmix 脚本生成

在 Jmix 中，我们通过对比 JPA 数据模型和数据库架构来生成 Liquibase 变更记录。开发周期主要由包含以下步骤：

1. 实现对象数据模型
2. 检查是否所有的变更记录都应用到了 DB
3. 对比对象数据模型和 DB 架构
4. 生成新的 Liquibase 变更记录，这些日志反映出对象数据模型与 DB 架构之间的差异 


看起来挺容易，是吧？Jmix 框架替我们做了所有繁重的工作。但是我们还是要保持谨慎，因为不是所有的变更都是安全的。给实体添加一个属性（即给表添加一个字段）一般来说是简单且安全的。但是，移除字段就需要谨慎一些，因为它可能导致数据丢失。

Jmix Studio 会对数据库更新进行分组并用红颜色显示出不安全的变更集，开发人员可根据需要从变更记录中将非安全变更移除。
 
变更记录在生成后即成为源码的一部分。在 Jmix 中，我们根据日期来对变更进行分组、存储。默认情况下，变更记录以下列文件夹结构存储：
 
所有新的变更记录会被放到对应的文件夹，同时会被包含到主 `changelog.xml` 文件。

Jmix 应用程序每次启动时会使用 Spring Boot Liquibase 集成 bean 来运行主变更记录。 

### 数据库迁移处理

在开发阶段，我们可以使用 Jmix Studio 将 Liquibase 变更记录应用到开发库。只需要在 Jmix 面板中的数据源节点调出右键菜单，然后选择 “Update Database”。  

在生产环境部署阶段，有时我们没有生产数据库的访问权限。这时，DevOps 或 DBA 有两个选项：

1. 在应用程序启动时自动应用变更记录
2. 手动应用变更记录然后重新部署并重启应用程序

如前面所述，在 Jmix 应用程序中，第一个选项是默认启用的。如果你不想应用程序自动更新数据库，需要在 `application.properties` 文件中将 `jmix.liquibase.enabled` 属性设置为 `false` 。 

Jmix 将变更记录与应用程序代码保存到同一个 JAR 文件。虽然 Liquibase 支持运行打包在 WAR 或 EAR 文件中的变更记录，但是不支持 Spring Boot JAR 的文件结构。如果你想单独执行数据库迁移，你需要手动将变更记录提取并拷贝到一个独立的位置，然后使用 Liquibase CLI 来运行。 

### 总结

数据库迁移是一项具有挑战性的任务。幸运的是有一些工具可以简化开发人员的工作。Liquibase 提供了变更记录执行、验证、完整性检查等功能。

使用 Jmix，你可以根据 JPA 模型变化来生成变更记录，然后对变更记录进行预览，如果需要，也可以执行更新。结合 Jmix 与 Liquibase，将可以覆盖从 JPA 实体创建到架构更新的整个数据库架构开发生命周期。
