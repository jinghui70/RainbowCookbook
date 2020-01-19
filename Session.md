# Session

对于外界的每一个请求，都是运行在一个线程中的。如果需要，系统提供了一个 Session 类，用于维护一个线程内的 Map<String, Object>。

不同于 J2EEServer，这个 Session 不是必须的，是否使用取决于系统设计者的想法。比如他可以把用户 ID 放在 Session 中，也可以在每个服务函数中增加用户 ID 参数而不用 Session。

Session 中的值应该在服务调用前设置好，如果访问时没有设置，会抛出 SessionException。
