# 数据库操作

所有的数据库操作，都被封装在接口`Dao`中。数据库配置中配置的每一个逻辑数据库连接，都会生成一个 Dao 对象实例，这个对象会读取对应的 rdmx 文件内容，实现对 O-R Mapping 的支持。

### 1. 简单的增删改

如果有一个人员表，有人员 ID 与 NAME 字段，经过 O-R Mapping，可以定义一个 Person 类如下：

```
public class Person {
    private String id;
    private String name;
    ...get/set
}
```

**向数据库中插入一个人员对象**

```
Person person = new Person();
p.setId(Utils.randomUUID64());
p.setName("韦小宝");
dao.insert(person);
```

**插入人员列表**

```
List<Person> list = Arrays.asList(person1, person2);
dao.insert(list);
```

**更新一个人员对象** `dao.update(person);`

**删除一个人员对象** `dao.delete(person);`

### 2. NeoBean

O-R Mapping 中的 Object，如果都对应的定义一个 Java Bean，有时并不那么美好，也没有必要。因此，一个特殊的 Map 对象 NeoBean 诞生了。它持有 Entity 的定义，它提供的 setValue，getValue 等函数可以根据 Entity 数据类型提供类型检查和转换，是一个很重要的对象。

- dao.newNeoBean("Person") 创建一个空的对应 Person Entity 的 NeoBean
- dao.makeNeoBean("Person", someObj) 也是创建一个对应 Person Entity 的 NeoBean，并把对象 someObj 中与 Person 的属性名相同的属性值设置给这个 NeoBean

### 3. 查询

dao.select() 返回一个 Select 对象，查询的写法看上去有点像 Sql。比如查询所有姓李的人员可以这样写：

```
List<Person> person = dao.select().from("Person").where("name", Op.like, "李%").queryForList(Person.class);
```

Select 对象实现了 ISelect 接口，这个接口描述了对查询结果的处理方式，可以参考 API 看一下具体的使用方法。

### 4. 条件更新与删除

同查询类似，dao.update() 返回一个 Update 对象，dao.delete()返回一个 Delete 对象，这些都比较简单，需要注意他们的执行都要调用一下自己的 execute 函数。

```
// 删除 id 为 001 的人员
dao.delete("Person").byId("001");
dao.delete("Person").where("id", "001").execute();

// 计划表有年份和编号两个字段做主键，删除2020年5号计划
dao.delete("Plan").byKey(2020, 5);
dao.delete("Plan").where("year", 2020).and("number", 5).execute();

// 所有人的岁数加1
dao.update("Person").set("age", '+', 1).execute();

// id为001的人员改名为张三并且年龄加1
dao.update("Person").set("name", "张三").set("age", '+', 1).where("id", "001").execute();
```
