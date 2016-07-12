#深入理解 binder 带着问题看 Binder
接着上一节的问题, 我们来详细讨论一下Binder
3个问题
1. 客户端`onServiceConnected` 得到的binder 对象到底是哪个对象呢?
2. 为什么 我们调用客户端 binder的`transact` 方法,而服务端那边的 binder 对象就会接着调用 `onTransact`方法呢?
3. 服务端执行 Binder 的线程是怎么一回事 

先看看这3个问题, 一点一点的明白 Binder 是怎么一回事