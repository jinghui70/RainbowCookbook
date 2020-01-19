# 配置文件

每个 Bundle 都可以有自己的配置文件。配置文件都统一存放在主控目录下的 conf 目录中，我们称它为配置主目录。配置主目录下，有平台的主配置文件`core.json`。

## 简单的配置需求

如果插件对配置的需求比较简单，可以在配置主目录下放置同名的 json 文件存储配置信息，也可以在配置主文件里加一个同名的配置项。

比如 com.sample.socket 插件需要配置启动端口和欢迎词，在配置主目录下建立名为`com.sample.socket.json`的配置文件，内容如下：

    {
        "port": 8888,
        "welcome": "欢迎光临"
    }

也可以在配置主文件 `core.json` 中加入以下内容：

    {
        "com.sample.socket": {
            "port": 8888,
            "welcome": "欢迎光临"
        }
    }

独立的配置文件与寄生在主配置文件中的效果是一样的，一般情况下建议使用独立的配置文件。平台提供了 ConfigData 类用来获取配置信息：

    ConfigData configData = activator.getConfig();
    int port = configData.getInt("port");
    String text = configData.getString("welcome");

上面的代码可以看到 ConfigData 是用 activator 获取的，那么如何获取 activator 呢？

写一个 Bean，派生自`ActivatorAwareObject` 或者实现`ActivatorAware`接口，activator 就会被自动注入。

在上面的例子中，如果定义一个 ServerConfig 类来对应配置内容：

    class ServerConfig {
        private int port;
        private String welcome;
        ... set/get
    }

现在希望直接把配置内容读取为这个类的一个实例对象，可以这样实现：

    @Bean
    class SampleCode extends ActivatorAwareObject implements InitializingBean {
        private ServerConfig serverConfig;
        @Override
        public void afterPropertiesSet() {
            serverConfig = activator.parseConfigFile(ServerConfig.class);
            ...
        }
    }

## 复杂的配置需求

当一个插件的配置文件不止一个，或者配置文件不是 json 格式时，需要在配置主目录下建立插件同名的子目录，把所有的配置文件都放在这个目录下。我们看到 `conf/db` 目录下就有数据库连接配置文件 `database.xml` 和数据模型描述文件 `xxx.rdmx` 等。BundelActivator 类提供类许多函数帮助获取配置信息：

    Path getConfigureFile(String fileName) // 获取指定名字的文件对象
    List<Path> getConfigureFiles(final String suffix) // 获取指定后缀的所有文件
    <T> T parseConfigFile(final String fileName, Class<T> clazz) // 把一个json格式的文件转为指定对象

## 开发期配置

有时候，开发时用到的配置与运行期不一样（比如开发的数据库连接与发布数据库连接是不同的），只需要在配置文件后面加上`.dev` 后缀就可以了，开发时会优先寻找该后缀的配置文件，打包发布的时候，这个后缀的文件都会被忽略掉。
