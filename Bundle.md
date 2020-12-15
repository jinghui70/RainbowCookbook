# 插件

## 基本概念

- 每个插件开发时对应的是一个 eclispe 的 project
- 插件的 id 就是 project 的名字
- src 根目录下应该有 bundle.xml 文件描述插件的信息
- 项目的根 package 与插件 id 一致，也就是与 project 名字一致
- 根 package 下有 Activator 类，这个是插件启动、停止的控制类，由平台控制

## bundle.xml

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<bundle>
	<id>插件的id</id>
	<desc>插件的描述</desc>
	<parent>db</parent>
	<parent>service</parent>
</bundle>
```

parent 定义的是依赖的其它插件，运行时，平台会把这些父插件加入当前插件的 classpath。开发时，因为没做解析这个描述文件的工作，所以还要手动的把插件包或者 project 加入开发的 build path

## Activator

- activator 是插件与外部世界的界面
- activator 维护了一个 Bean Context，插件启动时，所有单例 Bean 都会被加载。
- 重载 doStart 函数可以实现插件启动时的一些操作或者请求一些资源，一般我们在这里注册[扩展点](Extension.md)
- 重载 stop 函数可以实现插件停止时释放资源
