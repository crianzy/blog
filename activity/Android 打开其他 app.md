#Android 打开其他 app

##通过包名获取 启动的 intent  （推荐使用）

```java
     public static Intent getMainIntent(Context context, String packageName) {
          return context.getPackageManager().getLaunchIntentForPackage(packageName);
     }
    Intent intent = getMainIntent(getApplicationContext(), "com.zuimeia.suite.lockscreen.international");
    intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    intent.putExtra(EXTRA_LAYOUT_ID, themeid);
    startActivity(intent);
```
##通过包名和MainActivity 名字来启动：


```java

     Intent intent = new Intent(Intent.ACTION_MAIN);
     intent.addCategory(Intent.CATEGORY_LAUNCHER);         
     ComponentName cn = new ComponentName(packageName, className);         
     intent.setComponent(cn);
     startActivity(intent);
```
