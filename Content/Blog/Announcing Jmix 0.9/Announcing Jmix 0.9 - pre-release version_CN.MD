# Jmix 0.9 发布 - 预发行版

Jmix 0.9 是最新的预发行版，可以认为其 “几乎稳定”。同时我们也发布了最新版的配套开发工具 - Jmix Studio：0.9.1-202。

本次发布后，我们冻结了所有主要 API：数据模型定义、数据访问层、以及安全子系统。在 1.0 发布时这些会保持不变。因此，这个版本足以用于 MVP 开发。我们希望只需要很小的改动或不改动就可以从 0.9 迁移到 1.0。

我们来看看这个版本中各方面的一些重要功能：

## Jmix Studio

首先要提到的是：现在可以创建不同类型的 Jmix 项目。 在 0.9 版，Studio 提供了三种项目模板：

-   _Application - 应用程序_ - 创建新的 Jmix 应用程序。
-   _Theme - 主题_ - 为应用程序创建 CSS 主题。主题可以安装为扩展。
-   _Widget -部件_ - 这个类型的项目用于简化部件的创建。

为了辅助项目开发，Studio 提供了一个[专门的 Tool window](https://docs.jmix.io/jmix/0.x/studio/tool-window.html) 。可以用来导出 Jmix 特有的配置文件、在数据模型之间导航、配置数据源以及其他功能。

## Core

框架内核基于 Spring Boot 2.4.2，我们计划在 Jmix 1.0 发布时将 Spring Boot 更新到最新的 2.4.4。显然，在将来的版本发布中，我们会保持这种更新。

如之前所公布的一样，Jmix 使用 Liquibase 来处理数据库更新。与 CUBA 类似，框架会基于数据模型的变化来生成数据库更新脚本。但是与 CUBA 相比，现在更新脚本是 XML 格式，这样可以使用相同的脚本集去更新不同类型的数据库。

经验丰富的 CUBA 开发人员会注意到在应用程序中显式定义的 `User` 类，这看起来有点不同寻常。与 CUBA 平台相比，这是一个大的变化 - 我们决定为所有应用程序生成关键的用户管理功能类，这使得扩展和重写更简单。

## 后端（Backoffice） UI

现在，所有组件已经转换成使用 Jmix 框架的 API，并且已经可以在稳定版发布。需要注意的是，在 Jmix 中，后端 UI 是主项目的一部分，现在项目不再分成 `core` 和 `web` 模块。

默认情况下，生成的 Jmix 项目包含了后端 UI，其以依赖的方式添加，如果您不需要它，将其移除即可。Sutdio 中保留了大家熟悉的界面代码生成向导，所以您可以像以前一样轻点鼠标即可生成 CRUD 界面。

## React UI

React UI 客户端生成器得到了显著的改进。我们在持续开发 React UI 的支持库，以便简化在 Jmix 中用 ReactJS 进行开发的工作。现在您可以使用两个库： [Jmix React Core](https://docs.jmix.io/jmix-frontend-docs/0.x/jmix-react-core/index.html) - 用于处理 Jmix 实体、调用服务，还有 [Jmix React UI](https://docs.jmix.io/jmix-frontend-docs/0.x/jmix-react-ui/index.html) - 用于用户界面开发的组件集。

ReactJS 客户端的定制化比以前更简单了。和 Jmix 后端 UI 主题相似，您可以为前端 [创建主题](https://docs.jmix.io/jmix-frontend-docs/0.x/client-react/theme.html) 。

## 扩展

现在 Jmix 市场上有六个扩展：

-   REST API
-   地图 - Maps
-   动态属性 - Dynamic Attributes
-   实体日志 - Entity Log
-   电子邮件 - Email
-   实体探查 - Entity Inspector

我们持续地对已有扩展进行适配，很快您可以看到可用于 Jmix 0.9 的图表和 BPM 扩展。

## 总结

如果您一直在考虑采用 Jmix，是时候付诸行动了。API 已经稳定，所以您不会因为破坏性变化而需要重新实现您的 MVP。

同时，您可以享受新内核带来的便利性，可以使用 Spring Boot starter、更好部署选项，包括创建 Docker 镜像，保持像 CUBA 一样的开发速度。
