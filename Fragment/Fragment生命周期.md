##1.当一个fragment被创建的时候，它会经历以下状态.。

>onAttach()
onCreate()
onCreateView()
onActivityCreated()



##2.当这个fragment对用户可见的时候，它会经历以下状态。

>onStart()
onResume()

##3.当这个fragment进入“后台模式”的时候，它会经历以下状态。

>onPause()
onStop()


##4.当这个fragment被销毁了（或者持有它的activity被销毁了），它会经历以下状态。

>onPause()
onStop()
onDestroyView()
onDetach()

##5.就像activitie一样，在以下的状态中，可以使用Bundle对象保存一个fragment的对象。

>onCreate()     //保存的数据恢复
onCreateView()  //初始化UI
onActivityCreated()   //具体操作
     

##6.fragments的大部分状态都和activitie很相似，但fragment有一些新的状态。

>onAttached() —— 当fragment和activity关联之后，调用这个方法。
onCreateView() —— 创建fragment中的视图的时候，调用这个方法。
onActivityCreated() —— 当activity的onCreate()方法被返回之后，调用这个方法。
onDestroyView() —— 当fragment中的视图被移除的时候，调用这个方法。
onDetach() —— 当fragment和activity分离的时候，调用这个方法。





##7.在ViewPager 中如果 用户切换到 不同的 Fragment 则会执行如下方法:

```

public void setUserVisibleHint(boolean isVisibleToUser)

public void setMenuVisibility(boolean menuVisible)

```

上面两个方法是在FragmentPagerAdapter 中调用的,

一般都是通过重写 setUserVisibleHint 方法来判断 是否 当前页 被选中

















