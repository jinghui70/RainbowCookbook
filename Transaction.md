# 数据库事务

当多个数据库操作需要封装到一个事务时，只要将代码封装为一个 `Runnable` 即可，举例如下：

```
dao.transaction(new Runnable(){
    public void run() {
        dao.insert(...);
        dao.delete(...);
    }
});
```

建议用箭头函数的写法：

```
dao.transaction(()->{
    dao.insert(...);
    dao.delete(...);
});
```

有时候，一个事务成功完成后，需要返回一个值或对象，这时候数据库操作就不能封装为 `Runnable`，而要封装为能返回对象的 `Supplier<T>`。举例如下：

```
boolean result = dao.transaction(new Supplier<Boolean>(){
    Boolean get() {
        dao.insert(...);
        dao.delete(...);
        return dao.select(...).count()>10;
    }
});
```

或者：

```
boolean result = dao.transaction(()->{
    dao.insert(...);
    dao.delete(...);
    return dao.select(...).count()>10;
});
```

默认的事务处理级别是 TRANSACTION_READ_COMMITTED，如果需要改变处理级别，可以使用下面版本的函数：

```
    void transaction(int level, Runnable atom);
    <T> T transaction(int level, Supplier<T> atom);
```

level 的取值为以下四个值之一：

- Connection.TRANSACTION_READ_UNCOMMITTED
- Connection.TRANSACTION_READ_COMMITTED
- Connection.TRANSACTION_REPEATABLE_READ
- Connection.TRANSACTION_SERIALIZABLE
