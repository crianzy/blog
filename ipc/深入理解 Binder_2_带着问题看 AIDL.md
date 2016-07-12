#深入理解 binder 带着问题看 AIDL
上一节只是很浅显的讲了一下 AIDL的基本用法, 关于 AIDL 的内部过程的东西没有深入的讲解
这一节带着一些问题来深入看看 AIDL

1. 必须要 AIDL 文件吗?
2. 客户端调用AIDL方法是同步还是异步?
3. 服务端执行的 AIDL 方法 是在主线程吗?


##1. 必须是要 AIDL 文件吗?
答案是No, 不是必须的, 使用 AIDL 文件只是为了方便而已,IDE 能够给我自动生成一些代码,省去我们写代码的时间.
我们只需要 IDE 生成的那部分代码
上一节我们直接忽略了生成的代码细节,接下来就来详细看看IDE 生成的代码是什么:

```
// 定义一个接口 且继承 android.os.IInterface
public interface IMyAidlInterface extends android.os.IInterface {
    /**
     * Local-side IPC implementation stub class.
     * 基本Binder 类, 同时还实现 IMyAidlInterface接口, 该类是一个 abstract类, IMyAidlInterface 对应的方法需要子类去实现
     * 这个类 在服务端中 生产
     * Service onBinder 返回的一般就是 这个 Stub 的实现对象
     */
    public static abstract class Stub extends android.os.Binder implements com.czy.binderstudy.IMyAidlInterface {
        // 这个是 binder的 标识
        private static final java.lang.String DESCRIPTOR = "com.czy.binderstudy.IMyAidlInterface";

        /**
         * Construct the stub at attach it to the interface.
         * 构造方法 且吧 该对象 和 DESCRIPTOR 绑定在一起, 即如果在同一进程的话, 调用queryLocalInterface 方法返回不为空
         */
        public Stub() {
            this.attachInterface(this, DESCRIPTOR);
        }

        /**
         * Cast an IBinder object into an com.czy.binderstudy.IMyAidlInterface interface,
         * generating a proxy if needed.
         * <p/>
         * 吧 Binder 转换层对应 接口实现对象
         *
         * @param obj binder 对象
         */
        public static com.czy.binderstudy.IMyAidlInterface asInterface(android.os.IBinder obj) {
            if ((obj == null)) {
                return null;
            }
            // 更具 表示在本进程寻找是否已经存在对应接口实现对象, 如果在则直接返回的
            // 这里的判断 其实是 为了 判断客户端 和 服务端是否是在统一进程.
            // 如果在同一进程的话, 一定可以找到对应的接口实现对象, 就不需要构建Proxy 对象实现跨进程调用了
            android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
            if (((iin != null) && (iin instanceof com.czy.binderstudy.IMyAidlInterface))) {
                // 存在 强转返回
                return ((com.czy.binderstudy.IMyAidlInterface) iin);
            }
            // 不存在则 new 一个新的代理对象
            return new com.czy.binderstudy.IMyAidlInterface.Stub.Proxy(obj);
        }

        /**
         * @return 该类本身就是一个 binder 这里返回自己
         */
        @Override
        public android.os.IBinder asBinder() {
            return this;
        }


        /**
         * 这里 调用服务端的主要 方法
         *
         * @param code  对应的方法代码
         * @param data  对应的参数数据
         * @param reply 返回值数据
         * @param flags
         * @return
         * @throws android.os.RemoteException
         */
        @Override
        public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException {
            switch (code) {
                case INTERFACE_TRANSACTION: {
                    // 这里直接返回 binder 的表示
                    reply.writeString(DESCRIPTOR);
                    return true;
                }
                case TRANSACTION_basicTypes: {
                    // 更具表示, 来确定 参数解析的数据类型  调用的是 native 方法
                    data.enforceInterface(DESCRIPTOR);
                    int _arg0;
                    _arg0 = data.readInt();
                    long _arg1;
                    _arg1 = data.readLong();
                    boolean _arg2;
                    _arg2 = (0 != data.readInt());
                    float _arg3;
                    _arg3 = data.readFloat();
                    double _arg4;
                    _arg4 = data.readDouble();
                    java.lang.String _arg5;
                    _arg5 = data.readString();
                    //上面是解析参数
                    // 执行 具体的方法
                    this.basicTypes(_arg0, _arg1, _arg2, _arg3, _arg4, _arg5);
                    reply.writeNoException();
                    return true;
                }
                case TRANSACTION_getPid: {
                    data.enforceInterface(DESCRIPTOR);
                    int _result = this.getPid();
                    reply.writeNoException();
                    reply.writeInt(_result);
                    return true;
                }
                case TRANSACTION_addBook: {
                    data.enforceInterface(DESCRIPTOR);
                    com.czy.binderstudy.Book _arg0;
                    if ((0 != data.readInt())) {
                        _arg0 = com.czy.binderstudy.Book.CREATOR.createFromParcel(data);
                    } else {
                        _arg0 = null;
                    }
                    this.addBook(_arg0);
                    reply.writeNoException();
                    return true;
                }
                case TRANSACTION_getBook: {
                    data.enforceInterface(DESCRIPTOR);
                    com.czy.binderstudy.Book _result = this.getBook();
                    reply.writeNoException();
                    if ((_result != null)) {
                        // 这里 返回方法的 返回值
                        reply.writeInt(1);
                        _result.writeToParcel(reply, android.os.Parcelable.PARCELABLE_WRITE_RETURN_VALUE);
                    } else {
                        reply.writeInt(0);
                    }
                    return true;
                }
            }
            return super.onTransact(code, data, reply, flags);
        }

        //私有内部类 且自动帮我们实现了我们定义的 AIDL 接口方法
        private static class Proxy implements com.czy.binderstudy.IMyAidlInterface {
            private android.os.IBinder mRemote;

            Proxy(android.os.IBinder remote) {
                mRemote = remote;
            }

            @Override
            public android.os.IBinder asBinder() {
                return mRemote;
            }

            // 获取binder 表示
            public java.lang.String getInterfaceDescriptor() {
                return DESCRIPTOR;
            }

            /**
             * 接口的实现方法
             */
            @Override
            public void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat, double aDouble, java.lang.String aString) throws android.os.RemoteException {
                android.os.Parcel _data = android.os.Parcel.obtain();
                android.os.Parcel _reply = android.os.Parcel.obtain();
                try {
                    _data.writeInterfaceToken(DESCRIPTOR);
                    _data.writeInt(anInt);
                    _data.writeLong(aLong);
                    _data.writeInt(((aBoolean) ? (1) : (0)));
                    _data.writeFloat(aFloat);
                    _data.writeDouble(aDouble);
                    _data.writeString(aString);
                    // 把参数序列化 成 Parcel 对象
                    // 调用远程 binder 的 transact 方法
                    mRemote.transact(Stub.TRANSACTION_basicTypes, _data, _reply, 0);
                    _reply.readException();
                } finally {
                    _reply.recycle();
                    _data.recycle();
                }
            }

            @Override
            public int getPid() throws android.os.RemoteException {
                android.os.Parcel _data = android.os.Parcel.obtain();
                android.os.Parcel _reply = android.os.Parcel.obtain();
                int _result;
                try {
                    _data.writeInterfaceToken(DESCRIPTOR);
                    mRemote.transact(Stub.TRANSACTION_getPid, _data, _reply, 0);
                    _reply.readException();
                    _result = _reply.readInt();
                } finally {
                    _reply.recycle();
                    _data.recycle();
                }
                return _result;
            }

            @Override
            public void addBook(com.czy.binderstudy.Book book) throws android.os.RemoteException {
                android.os.Parcel _data = android.os.Parcel.obtain();
                android.os.Parcel _reply = android.os.Parcel.obtain();
                try {
                    _data.writeInterfaceToken(DESCRIPTOR);
                    if ((book != null)) {
                        _data.writeInt(1);
                        book.writeToParcel(_data, 0);
                    } else {
                        _data.writeInt(0);
                    }
                    mRemote.transact(Stub.TRANSACTION_addBook, _data, _reply, 0);
                    _reply.readException();
                } finally {
                    _reply.recycle();
                    _data.recycle();
                }
            }

            @Override
            public com.czy.binderstudy.Book getBook() throws android.os.RemoteException {
                android.os.Parcel _data = android.os.Parcel.obtain();
                android.os.Parcel _reply = android.os.Parcel.obtain();
                com.czy.binderstudy.Book _result;
                try {
                    _data.writeInterfaceToken(DESCRIPTOR);
                    mRemote.transact(Stub.TRANSACTION_getBook, _data, _reply, 0);
                    _reply.readException();
                    if ((0 != _reply.readInt())) {
                        _result = com.czy.binderstudy.Book.CREATOR.createFromParcel(_reply);
                    } else {
                        _result = null;
                    }
                } finally {
                    _reply.recycle();
                    _data.recycle();
                }
                return _result;
            }
        }

        // 接口方法 对应的 id, 在onTransact 方法更具这些id 来确定到底实现哪个方法
        static final int TRANSACTION_basicTypes = (android.os.IBinder.FIRST_CALL_TRANSACTION + 0);
        static final int TRANSACTION_getPid = (android.os.IBinder.FIRST_CALL_TRANSACTION + 1);
        static final int TRANSACTION_addBook = (android.os.IBinder.FIRST_CALL_TRANSACTION + 2);
        static final int TRANSACTION_getBook = (android.os.IBinder.FIRST_CALL_TRANSACTION + 3);
    }

    // 下面4个方法 就是我们在 AIDL 文件中定义的接口方法
    public void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat, double aDouble, java.lang.String aString) throws android.os.RemoteException;

    public int getPid() throws android.os.RemoteException;

    public void addBook(com.czy.binderstudy.Book book) throws android.os.RemoteException;

    public com.czy.binderstudy.Book getBook() throws android.os.RemoteException;
}

```
关于该 java 文件的一些解释,都写在 java 代码上, 代码与注释结合着看更能理解一些.
其实我们只需要 这个 java 文件就可以了, 完全可以不写 AIDL 文件
只要我们按照上面的代码规则来, 完全可以不需要 AIDL 文件, 不过那样有些麻烦.

更具上面的代码在回顾一下上一节的内容,
在 Service 中 onBinder 方法中返回的是 Stub 类的实现对象, 
然后 客户端中, onBinder 方法的第二参数 ServiceConnection中有一个 回调方法
`void onServiceConnected(ComponentName componentName, IBinder iBinder)`
该方法的第二个蚕食是一个 Binder 对象, 我们通过该对象 调用`Stub.asInterface(iBinder)`方法即获得 AIDL 接口实现对象.

接着我们去 `asInterface` 方法中看看
先调用 `queryLocalInterface`方法查看该 Binder内部是否已经有对应的实现对象了.
这时候 如果客户端 和 服务端 是在同一一个进程, 那么 其实 `onServiceConnected` 方法中的 Binder 对象 和 Service 中的 onBinder 返回的对象是一个对象,
所以`queryLocalInterface`返回值不为空

如果客户端和服务端不在同一进程, 那么则会 new 一个 Stub.Proxy 对象, 该对象实现了 AIDL 接口.

所以我们在客户端中的 onServiceConnected 方法中 通过 `Stub.asInterface(iBinder)`方法即获得 AIDL 接口实现对象就是  Stub.Proxy 对象.

那么我调用给该接口的方法, 其实调用的是 Stub.Proxy 对象 中的方法.

我们继续去看 Stub.Proxy 中实现的 AIDL 接口方法

实现方法的内容基本一致, 过程如下
1. 把参数序列化
2. 调用 `onServiceConnected` 中的第二个参数 Binder 对象的 transact 方法, 紧接着另一个进程中就会调用binder onTransact方法
3. 读取 transact 方法中的_reply 参数, 并反序列化成 java对象 并返回反序列后的 java 对象

ok 跨进程调用就结束了

看到这里 似乎有点清晰, 但是感觉还是挺模糊的....
是这样的....  上面的过程基本就是跟着代码一步一步的走.
但是走的有点迷糊...


首先我们要知道, 连个进程之间一般情况 内存之间是不会有交集的, 各自的内存区域是独立的,  各自的对象是独立
不能再两个进程之间 共享同一个对象.

那么 我们在看到的, 在客户端中看到的`onServiceConnected`中的第二个参数 Binder 对象 是不是 Service OnBinder 方法中返回的那个对象呢?
如果客户端与服务端不在同一个进程, 那么可以肯定的回答 不是.

那个我们在 客户端`onServiceConnected` 得到的binder 对象到底是哪个对象呢?
为什么 我们在客户端调用它的`transact` 方法,而服务端那边的 binder 对象就会接着调用对应的 `onTransact`方法呢?

这个问题有些复杂, 我们下一节在详细阐述

## 2. 客户端调用AIDL方法是同步还是异步?
更具上面我们分析的代码来看, 客户端这边似乎没有开辟新的线程, 在transact 有没有开辟新的线程暂时还不知道.
但是在我们执行 AIDL 方法的时候, 至少没有回调的概念, 数据都是直接返回的.没有回调这回事
所以这里是同步的,
既然是同步的, 那么问题来了.
我们在进程A 的主线程执行 AIDL 方法, 那么在进程B 中真正执行该方法的线程也是主线程吗?

## 3. 服务端执行的 AIDL 方法 是在主线程吗?
答案是 No
服务端执行的 AIDL 方法一定不是在主线程.
不信, 自己可以打 log 看看.
但是这是 为什么? 
首先由明白 这是两个进程, A 进程中的线程 与B 进程中的线程没有任何关系
__所以不要以为 A 进程在主线程执行, B 进程也要在主线程中执行__
如果看过 AMS 源码的就知道, 每次 AMS 想调用 ActivityThread 中的方法的时候, 
都是通过 Binder 调用ActivityThread scheduleXXXX 方法(如: schedulePauseActivity)
但是在这些方法内部 都是 handler 发送一个 Message, 然后主线程在获取Message, 在执行相应的方法
因为想操作UI 必须在主线程,但是 Binder 中执行的接口方法一定不再主线程.
那在哪个线程呢?
在 Binder 自己开辟的一个线程中.
具体在怎么开辟的,为什么,下一节再详细阐述

这一节 通过3个简单而不普通的问题切入, 但是带来了更多复杂而深远的问题

总结一下带来的问题:
1. 客户端`onServiceConnected` 得到的binder 对象到底是哪个对象呢?
2. 为什么 我们调用客户端 binder的`transact` 方法,而服务端那边的 binder 对象就会接着调用 `onTransact`方法呢?
3. 服务端执行 Binder 的线程是怎么一回事 

除了问题而且还发现一点, 似乎怎么 AIDL 跨进程调用都离不开 binder.
客户端的 binder 调用某个方法, 接着服务端的 binder 就回调某个方法
那么
__Binder 到底是怎么一回事?__
请看下节













