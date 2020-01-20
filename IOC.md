# 依赖注入

依赖注入（Dependency Injection），或者叫控制反转（Inversion of Control），是 Spring 最早的基础功能之一。Rainbow 借鉴了早期 Spring 的代码，自己定义了一个简单的 IOC 容器。

容器中存放的东西，被称为 Bean。Bean 有两种，一种叫单例 Bean，容器只会为它创建一个实例，每次获取这个 Bean，得到的都是同一个对象；另一种叫原型 Bean，每次获取这个 Bean，容器都会创建一个新的实例，并为其注入所有的依赖。

如果熟悉 Spring，这些都是基本概念。并且 Bean 如果实现`InitializingBean`、`DisposableBean` 这些接口，概念和 Spring 是一致的。

因为简化了的原因，我们只支持属性注入，这意味这每个 Bean 都必须有无参数的缺省构造函数。在需要注入依赖的属性或者 set 方法上，加上`@Inject` 注解即可。

## Bundle 的 Bean 容器

每一个 Bundle，都是由 Activator 管理生命周期的，平台启动 Bundle 时，会创建其 Activator 的实例，Activator 维护了一个 IOC 容器，称之为 Bundle 的 Bean 容器。

只要在一个类的定义上面添加 `@Bean` 注解，就可以配置为容器中的 Bean。

- 配置一个单例 Bean，并设置 BeanName 为 sample：

```
@Bean(name="sample")
public class SampleSingleton {
}
```

- 配置一个 原型 Bean

```
@Bean(singleton=false)
public class SamplePrototype {
}
```

如果不设置 BeanName，默认的 BeanName 为第一个字母小写的类的名字，上面的例子中就是 samplePrototype。

### 几个重要的点：

- 有一个特别的约定，如果类名以 Impl 为结尾，默认的 BeanName 会忽略它。比如 UserServiceImpl 这个类的 BeanName 为 userService。
- 插件是单向依赖的，插件的容器在为其 Bean 注入依赖的时候，如果在自己的容器中找不到，会到其上级插件容器中寻找。
- 有时候某个 Bean 需要访问插件的 Activator(参见[访问配置文件](Config.md))，如果它实现了 `ActivatorAware` 接口或者派生自 `ActivatorAwareObject`，容器会把插件的 Activator 注入给它。
- 如果一个 Bean 同时是一个[扩展](Extension.md)，那么它无需手动注册，只要在注解中标示出来就可以了。

另外需要了解的是，。

## Bean 容器的底层实现

以下内容揭示类容器的实现原理，可以参考看一下，不用深究。

- 最基本的容器是`Context`类实现的，Bean 的配置使用一个`Map<String, Bean>`作为参数传递给 Context 的构造函数。
- `ApplicationContext` 派生自 `Context`，不同的是它注册了一个 `InjectProvider` [扩展点](Extension.md)。实现了该扩展点的扩展可以把外部对象注入到有需要的 Bean 中。比如数据库访问对象 `Dao` 由 `DaoManager` 管理，所以有一个 Dao 的 InjectProvider 扩展，这样，ApplicationContext 发现某些 Bean 需要注入 Dao 对象时，就会找到这个扩展来获取需要注入的对象。
- Bundle 的容器 `BundleContext` 派生自`ApplicationContext`
