ContentProvider 是原生支持的 跨进程通信的

我们在使用 contentProvider 的基本过程如下:



第一步，它从ActivityThread里面本地缓存寻找ContentProvider对象，所以找到了，就一切ok..

第二步，如果第一步没有找到，那么就去ActivityManagerService远程服务中寻找ContentProvider对象。

第三步，从远程服务中找到ContentProvider对象之后，就把这个对象缓存在本地，那么下次找的话，直接就可以从本地缓存中查找了。



#线程安全考虑

从上面一段描述来看，我们可以发现一个问题，ContentProvider在某种程度上是单例的，比如我们第一次从本地map缓存里面得到ContentProvider对象，第二次我们在同一个应用程序请求的时候，拿到的肯定是同一个缓存对象。


所以，ContentProvider只能配置进程之间是否是单例，同一个进程里面是不能配置是否是单例的，因为它在同一个进程里面肯定是单例。

配置进程之间是否是单例:

```

android:multiprocess="true"

```

所以我们的ContentProvider的代码，比如查询，更新，删除等等，必须注意线程安全的问题。

那么单例下，我们怎么注意线程安全问题呢？

1. ContentProvider尽量少用成员变量，因为我们用的是单例，所以成员变量是共享的。

2. 所以真的用到了共享资源，建议用synchronized或者TheadLocal来解决。





我们在注册 ContentProvider 的时候 有一个选项 android:multiprocess 

表示说, 是否在多进程键 也只使用一个单例

这样可以减少 进程间的通信 contentProvider 对象间的同步开销

>`android:multiprocess`

Whether or not an instance of the content provider can be created in every client process — "true" if instances can run in multiple processes, and "false" if not. The default value is "false".
Normally, a content provider is instantiated in the process of the application that defined it. However, if this flag is set to "true", the system can create an instance in every process where there's a client that wants to interact with it, thus avoiding the overhead of interprocess communication.

