# 建立开发环境

Rainbow 开发是建立在 Eclipse 环境下的，准备开发一个系统的时候，首先要用 Eclipse 为这个系统建一个 Workspace，这个系统的所有代码都在这个工作空间的目录下，保证所有开发人员的开发环境的一致性。这样，每当有新人加入团队开发这个项目的时候，只需要把别人的 Workspace 目录拷贝过来即可，什么都不用改。

## Rainbow 的开发设定

- 使用 Ant 打包部署管理并开发中的一些问题，不使用 Maven。
- 统一管理第三方 jar, 不使用 Maven。
- 使用 Slf4j/Logback 记录日志

## 主控项目

Workspace 中有一个名为 rainbow 的主控 Java Project，也是平台的入口所在，其目录结构及说明如下：

- `src` 这是项目的 Source Folder，放置开发时的日志配置文件 logback.xml
- `conf` [配置文件](Config.md)目录
- `build.xml` Ant 构建脚本
- `library.json` 第三方 Jar 文件描述

以下目录及文件是生成出来的，不需要放进 Source Control 系统

- `[build]` 打包用的文件目录
- `[bundle]` 预置插件目录
- `[lib]` 第三方 jar 以及`core.jar`所在目录
- `[deploy.zip]` Rainbow 平台部署版本
- `[dev.zip]` Rainbow 平台开发版本
- `[version.txt]` Rainbow 平台的版本

## build.xml

在 Eclipse 中，点击菜单 Window->Show View->Ant, 打开 Ant View，并把 build.xml 拖进这个 View 中，可以看到这个构建脚本的所有命令：

- library：这个命令会根据 library.json 中的配置下载所有的第三方 jar 到 lib 目录下，并生成一个 rainbow.userlibraraies 文件，在 Preferences 中选择 Java->Build Path->User Libraries，点击 import 导入这个文件，就可以配置好一个名为 Rainbow Library 的用户库，所有的插件工程的 ClassPath 都需要包括这个用户库。
- upgrade-rainbow：这个命令会下载最新版本的 Rainbow 平台文件（deploy.zip 和 dev.zip），并在主控目录下解压 dev.zip，同时调用 library 命令。
- deploy：这个命令将打包整个系统为一个部署目录
- makeDevelopDatabase：这个命令根据数据库设计及测试数据生成开发用的数据库
