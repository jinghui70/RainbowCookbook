# 服务开发

服务开发是 Rainbow 的一个重点，service 插件实现了对服务的管理。

基本上大部分的开发工作，都是开发服务，那么什么是服务呢？简单的说，就是 Java 的接口，我们约定以 Service 作为名字后缀的 Java 接口，就是服务的定义；以 ServiceImpl 作为名字后缀的 Java 类，就是服务的实现，它必须实现对应的接口，服务实现类要标记@Bean，由插件容器管理。

服务的接口与实现，都要注册在服务注册表上。当写一个新的服务插件时，首先要依赖 service 插件，然后 Activator 派生自 ServiceBundleActivator 就可以了，这个 Activator 会自动发现所有的服务与实现并完成注册工作。