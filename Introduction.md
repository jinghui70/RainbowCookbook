# 基本结构

## 狭义的 Rainbow

从狭义的角度来说，整个平台就是一个核心的包`core.jar`，只有 300 多 K，它提供了以下的内容：

- [插件管理机制](Bundle.md)
- [扩展机制](Extension.md)
- 一些基础的工具类

除了`core.jar`以外，所有的代码都以插件的形式存在。相关功能的 java 代码组成一个插件，我们称之为 Bundle。Bundle 在部署运行状态是一个 jar 文件，存放在 bundle 目录下；在开发状态，每个 Bundle 就是一个 java 工程。

Rainbow 的 Bundle 与 Eclipse 的 plugin 概念是一样的，Eclipse 有各种各样的 plugin，第三方也可以开发自己的 plugin 放在 Eclipse 的 plugins 目录下，这是 Eclipse 如此强大的原因之一。

Rainbow 复刻了 Eclipse 的插件机制和扩展机制并做了简化，也是希望和 Eclipse 一样灵活且强大。

## 广义的 Rainbow

从广义的角度来说，Rainbow 平台还提供了许多平台级的基础插件，形成了自己的生态系统。这些插件包括：

- [数据库访问插件](Database.md)
- [服务管理插件](Service.md)

未来还会提供更多的基础插件，让开发人员聚焦于业务开发，不用在系统的管理配置上浪费时间。
