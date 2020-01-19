# 查询条件

查询条件是用 C 对象来封装的。查询操作符定义为一个枚举 Op：

```
public enum Op {
	Like(" like "),
	NotLike(" not like "),
	Equal("="),
	NotEqual("<>"),
	Greater(">"),
	Less("<"),
	GreaterEqual(">="),
	LessEqual("<="),
	IN(" in "),
	NotIn(" not in ");
}
```

## 简单条件

```
C.make(); // 定义一个空条件
C.make("qty", 100); // 定义一个条件，qty = 100，默认的 Op 是 Equal
C.make("qty", Op.Less, 100); // qty < 100
```

## 组合条件

简单条件可以通过 and or 链接起来成为组合条件：

```
C.make("qty", 100).and("name", "111");
```

当需要用 or 链接两个组合条件时，写法如下：

```
C.make(...).and(...).or(C.make(...).and(...));
```
