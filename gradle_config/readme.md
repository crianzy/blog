# 通过 gradle 自动配置一些参数

gradle 的功能真的是不可谓不强大, 我们可以在 gradle 做出很多自定义的配置
下面主要讲一下我在工作中遇到的一些配置项

## 前提
要做到,我们在 gradle做了一些配置,然后我们在 java 代码中能够读取到对应的配置值, 这点很重要.
这里就需要用到 `BuildConfig` 则个类了

`BuildConfig` 这个类是 gradle 在编译的过程中自动生成的一个类

先看看我想买中生成的`BuildConfig`文件:
```
public final class BuildConfig {
  public static final boolean DEBUG = Boolean.parseBoolean("true");
  public static final String APPLICATION_ID = "com.zuiapps.zuiworld";
  public static final String BUILD_TYPE = "debug";
  public static final String FLAVOR = "normal";
  public static final int VERSION_CODE = 10002;
  public static final String VERSION_NAME = "1.0.2.dev";
  // Fields from product flavor: normal
  public static final String API_BASE_URL = "http://design.zuimeia.com/";
  // Fields from default config.
  public static final String CHANNEL = "";
}
```

这个类是直接可以在代码中引用的, 同样我们在 gradle 文件中做出的一些配置也会反应到这个文件中.

下面举三个我在项目中遇到的自动配置例子

## 例子1: 自动开关 Log

一般我们在打 release 包的时候都需要吧 Log 给关掉,
对了这里我也相信大多数的项目中都有 LogUtil 类似的这个一个类, 里面能够设置 log 开关什么的

这里我们就可以用到 `BuildConfig.DEBUG` 来判断是否是 release 包, 从而做到 log 的打开与关闭
```
LogUtil.setLogEnable(BuildConfig.DEBUG);
```

## 例子2: 自动配置api 的测试环境 和正式环境



