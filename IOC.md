# 依赖注入

依赖注入（Dependency Injection），或者叫控制反转（Inversion of Control），是 Spring 最早的基础功能之一。Rainbow 借鉴了早期 Spring 的代码，自己定义了一个简单的 IOC 容器，源码在 Core 的 util/ioc package 下。

容器中存放的东西，被称为 Bean。Bean 有两种，一种叫单例 Bean，容器只会为它创建一个实例，每次获取这个 Bean，得到的都是同一个对象；另一种叫原型 Bean，每次获取这个 Bean，容器都会创建一个新的实例，并为其注入所有的依赖。

如果熟悉 Spring，这些概念应该很好理解。并且`InitializingBean`、`DisposableBean` 这些接口都和 Spring 是一致的。

## 最基本的容器

最基本的容器是`Context`类实现的，因为是简化版本的 IOC，要求只能通过 set 函数做依赖注入，并且该函数上需要用`@Inject`注解标记这是需要依赖注入的属性。

Context 是一个标准的容器，通过一个`Map<String, Bean>`来配置。

## 平台级的容器

`ApplicationContext` 派生自 `Context`，不同的是它注册了一个 `InjectProvider` [扩展点](Extension.md)。有些平台级别的对象并不是用容器来管理的，比如数据库访问对象 `Dao` 是所有需要访问数据库的 Bean 都要注入的依赖， `DaoManager` 管理所有的 Dao，并注册了一个 InjectProvider 扩展，这样，ApplicationContext 发现某些 Bean 需要注入 Dao 对象时，就会找到这个扩展来获取需要注入的对象。

## 插件的容器

- Context 是容器最底层的实现，一般情况下不会直接用到。
- ApplicationContext 继承了 Context，增加了平台对象的注入能力，但是一般情况下也不会直接用到。
- BundleContext 派生自 ApplicationContext，这是开发最直接用到的容器。

在 Bundle 中定义一个 Bean，只需要在它的类定义前面加上 @Bean 标记，就会自动的被放进 BundleContext 中。

我们知道每个插件都有一个管理生命周期的类 Activator，它的实例是平台创建的，在启动函数 start 中，它遍历插件中所有的类，收集所有标有 @Bean 标记的类并创建 BundleContext，如果某个 Bean 实现了 ActivatorAware 接口或者派生自 ActivatorAwareObject，BundleContext 会把插件的 Activator 注入给它。

另外需要了解的是，插件是单向依赖的，某个插件中的 Bean 是可以注入其上级插件容器中的 Bean。

IOC 是一个重要的概念，IOC 容器统一管理了所有 Bean 的生命周期。在面向服务的开发中，服务实现类一般都是单例的，实现单例模式最简单的办法就是把它定义为一个单例 Bean。
