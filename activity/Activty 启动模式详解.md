#Activty 启动模式详解


# Activity 启动模式设置方式

通过设置 launchMode 属性

```
<activity
   android:name=".main.view.SplashActivity"
   android:launchMode="singleTask">
</activity>
```



#Android 有4种启动模式

- standard               默认的启动模式,每次都启动一个新的Activity
- singleTop              当站定的Activity与要启动的Activity 是同一个时, 则不启动新的Activity
- singleTask             把启动的Activity的Activity不在栈顶, 那么会把栈顶的Activity砍掉, 走onNewIntent方法
- singleInstance         只存在一个实例,且在单独的栈中


## standard(默认)
默认的启动模式, 每次都是打开新的Activity



## singleTop
当要打开的Activity的在栈顶时, 不打开新页面,会调用当前栈顶Activity的 onNewInetent() 方法
如果不在栈顶 则打开一个新的 Activity



##singleTask

###当如果在栈中存在该Activity时

则把该Activity 上面的所有Activity 都移除,  而且还会调用 onNewInetent() 方法

例子如下:

假设  A 的启动模式 是 singleTask
>当前栈中  有  B C A C D, 栈顶是D

当D 要启动 A 时,  由于 A 是 singleTask

那么 系统会把 A 上面的  CD 都移除

然后调用 A 的 onNewInetent 方法

处理后的结果是:

>当前栈中  有  B C A , 栈顶是A

###如果当前栈中 不存在 该Activity时

则新建一个Activity

##singleInstance

表示栈中只有一个 我一个Activity 不能存在其他Activity

示例:

>假设 A,B,  是普通的standard 启动模式, C 是 singleInstance 启动模式, D 是 singleTask启动模式


>1. A -> B 正常

>2. A -> B -> C 正常启动C, C在一个单独的栈中

>3. A -> B -> C -> D正常启动D, D 与AB 在一个栈中 

>但是在

>D 按返回的时候  回到的是B,
>B 按返回的时候  回到的是A
>A 按返回的时候  回到的是C
>4. A -> B -> C -> D -> C 这里没有重新创建C 走了C 的onNewIntent 方法

>返回的路线是: C -> D -> B -> A


由以上例子可以得出以下结论:

- 如果启动的Activity是 singleInstance 那么他会被放到另外一个栈中去,

- singleInstance 的这个 Activity 所启动的这个Activity 与它不在同一个栈中, 即上面的步骤3  D 与AB 在一个栈

- 如果要启动的这个singleInstance 的Activity已经启动了, 那么就不会重新创建了 会走 onNewIntent 方法 上面步骤4