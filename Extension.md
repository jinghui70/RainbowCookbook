# 扩展与扩展点

当有一类相似的事情需要统一管理的时候，而这类事情未来还可能会增加，这就是使用扩展机制的好时机。我们现在的代码中有很多用到扩展的例子：

- 任务调度
  调度只负责处理任务执行相关逻辑，具体任务类型有很多，而且未来还会增加。只要定义任务类型扩展点，未来新增的插件中实现这个扩展点定义的接口，就可以自动增加新的任务类型，该类型的任务就可以加入任务池由调度管理了。
- 待办事项
  待办事项与任务调度相似，待办事项类型扩展点定义了管理规范，具体实现的扩展处理各自个性化的业务。
- 字段加工厂(Refinery)
  针对不同的字段类型做加工，比如日期型的可以做计算后转义，代码型的可以翻译，随着需要可以随时增加新的加工厂。这样查询的时候就可以有更丰富的结果了。

扩展可以存在于不同于扩展点的 Bundle 中，所以未来的扩展是通过不断的增加插件来完成的，这样做的好处是把处理机制和具体的处理逻辑做了很好的解耦，系统可以无限扩展而不用改动原有代码。

## 扩展点定义与使用

扩展点定义的是要管理的规范，一般情况下就是一个简单的 java 接口。建议这个 java 接口实现 INameObject，这样每个扩展都有一个 name 属性，将来可以用名字来找对应的扩展。

```
    public interface SampleExtensionPoint extends INameObject {
        .....
    }
```

定义好扩展点接口，需要在插件的 Activator 中注册这个扩展点。

```
	protected void registerExtensionPoint() throws BundleException {
        registerExtensionPoint(SampleExtensionPoint.class);
	}
```

使用的时候，ExtensionRegistry 提供了很多获取扩展的函数，可以根据 name，或者 class 来获取指定的扩展对象，也可以获取所有已注册的扩展名字或对象列表。下面是最常用的三个函数：

    ExtensionRegistry.getExtensionObject(SampleExtensionPoint.class, "E") 得到name为E的SampleExtensionPoint对象
    ExtensionRegistry.getExtensionObjects(SampleExtensionPoint.class) 得到 List<SampleExtensionPoint>
    ExtensionRegistry.getExtensionNames(SampleExtensionPoint.class) 得到 List<String>

## 扩展实现

具体的扩展对象就是一个类，只要实现了扩展点的接口，注册在平台扩展注册表就可以了。
扩展必须是单例的，最简单的办法就是定义它为一个单例的 Bean，并在 @Bean 里面注明它实现了哪个扩展点接口。

    @Bean(extention=SampleExtensionPoint.class)
    public class E implements SampleExtensionPoint {
        @Override
        public String getName() {
            return "E";
        }
        ....
    }
