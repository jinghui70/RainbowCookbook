# 多表关联

虽然我们提倡写简单的 sql，也有一些技术和技巧可以避免关联操作。但是有时还是免不了需要进行多表关联查询。

表关联是数据库的概念，数据库只是数据保存的一种古老的方法，随着大数据应用的发展，数据存储也有了更多的方式。所以结构化数据从概念上，是可以抽象为对象与属性的，这样才能适应更多的底层数据操作要求。

关联关系可以抽象为一个对象的特殊属性，把两个数据对象链接起来，我们称为链接属性，对于主对象来说，它原来的一个（或几个）基本数据类型的属性被定义为一个链接属性，指向另一个数据对象。

## 关联定义

两个表的关联关系可以在`rdmx`中进行定义，比如`人员表 T_RY`有一个`所属部门ID`字段，那么可以为`人员表 T_RY`建一个链接属性如下图所示:

<img src="\assets\link.png" />

当要查询人员信息时，可以用 `'.'` 访问链接属性的子属性：

```
dao.select("id,name,org.name").from("Person").where("org.name", Op.like, "%北京%")
```

上面的例子是返回机构名称中含有 **北京** 的所有人员 id,人员 name 和该人员所属机构的 name。链接属性的子属性可以加别名作为返回数据的属性名：

```
dao.select("id,name,org.name:orgName").from("Person")
```

## 一对多关联

人员表与家庭成员表是一对多关系，可以选择人员表的 id 属性关联家庭成员表的人员 id 属性，建立一个链接属性，同时把列表属性标记选上。
<img src="\assets\link_list.png" />

这时候查询 family 属性 依然是用 `.` 来指定子属性，用 select 查询时写法上没什么区别。

## 多对多关联

多对多关联，应该用一个中间表将其转化为两个一对多关联。如果要对这三个对象进行关联，应以中间表为主对象。

## 临时关联

定义关联关系，是一个需要仔细考虑的工作，有些关联关系可能由于各种原因并未定义在模型中，我们可以在查询时临时增加关联定义来解决问题。参看 Select 源码的 extraLink 函数。

```
	/**
	 * 添加一个并未定义在rdmx模型里面的额外的链接
	 *
	 * @param name link的名称
	 * @param fields 主对象属性列表，以逗号分割
	 * @param targetEntityName 链接对象名
	 * @param targetFields 链接对象属性列表，以逗号分隔
	 * @return
	 */
	public Select extraLink(String name, String fields, String targetEntityName, String targetFields)
```

## 需要注意的问题

通过链接属性的查询，如果链接对象的查询条件写在 where 子句中，因为默认的是 LeftJoin，当链接对象条件不满足时，主对象也查不出数据。可能这并不是想要的结果，这时可以把链接对象的条件用下面的函数来写：

```
    Select setLinkCnds(String link, C cnd)
```