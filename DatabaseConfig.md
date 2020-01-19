# 连接数据库

在 db 插件的配置目录下，有一个 database.xml 文件，用来配置数据库的连接参数。

一个典型的配置文件如下：

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<config xmlns="http://rainbow/db/config">
	<physic id="DB_SAMPLE">
		<driverClass>org.h2.Driver</driverClass>
		<jdbcUrl>jdbc:h2:../db/DB;AUTO_SERVER=TRUE;IFEXISTS=TRUE</jdbcUrl>
		<username>sa</username>
		<password></password>
	</physic>
	<logic id="DB" model="MyModel" physic="DB_SAMPLE" />
</config>
```
