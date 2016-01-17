---
layout: post
title: Android开发中发现的一些小技巧 —— 字符串相关
description: Android开发中发现的一些小技巧 —— 字符串相关
category: blog
---

# Android开发中发现的一些小技巧 —— 字符串相关

这篇文章是对我这段时间开发Android项目用到的一些小技巧的总结，相信也有不少的人知晓或用过这些技巧。我在这里仅进行一次小小的抛砖，希望能给奋战在Android开发战线的朋友一些帮助。


相信不少朋友在接手到旧的项目的时候相信util包下都有让人眼花缭乱的工具类。其实很多工具类是在重复的造Java/Android给我们提供的轮子。其实很多常用的功能早就帮我们做好了。

1、android.text.format.DateUtils
这个类是Android提供的格式化日期的工具类。相对于我们经常使用的`SimpleDateFormat`类来说，`DateUtils`已经处理好**国际化**相关的问题，而且还封装了一些很简便的方法。

- public static String formatDateTime(Context context, long millis, int flags)；
这个方法可以格式化日期和时间，接收三个参数，其中第三个参数`flags`决定输出后的格式，如果只输出日期的话：

    DateUtils.formatDateTime(context,millis,DateUtils.FORMAT_SHOW_DATE)

如果想输出年或月等等格式的话，就在`DateUtils`找对应的flags就可以。但是如果想输出年、月、日、星期、日期等多重的结果的组合，可以通过`|`操作符来运算。

	    DateUtils.formatDateTime(context, millis,
	    	DateUtils.FORMAT_SHOW_YEAR |
	    	DateUtils.FORMAT_SHOW_DATE |
	    	DateUtils.FORMAT_SHOW_WEEKDAY |
	    	DateUtils.FORMAT_SHOW_TIME
	    )

输出的结果：`2016年1月16日星期五 12:40`

- public static boolean isToday(long when)  判断日期是否是今天

- public static CharSequence getRelativeTimeSpanString(long startTime)  返回与当前的相对时间的字符串。这样说有些抽象，比如微信的聊天列表上显示的上一条信息的时间，如果是今天就显示今天的时间，是昨天的就显示昨天，再久远一点的就显示日期（但是这个方法的最大只能精确到天，不能像微信那样显示到几秒前，几分钟前）。

中文状态下的输出：
![](http://7xprht.com1.z0.glb.clouddn.com/S60116-124009%5B1%5D.jpg)

在调整系统设置为英文的状态下输出的结果：
![](http://7xprht.com1.z0.glb.clouddn.com/S60116-124056%5B1%5D.jpg)

2、android.text.TextUtils

- public static boolean isEmpty(@Nullable CharSequence str)  这个方法判断字符串非null且非空，在对字符串进行判断时可以不必`if(str !=null && str.length() > 0)`这样判断。
- public static CharSequence concat(CharSequence... text) 字符串连接，其中用`StringBuilder`实现，进行字符串拼接时可以考虑使用这个方法。而且支持`SpannableString`。 
- public static boolean isDigitsOnly(CharSequence str)  判断字符串是否是纯数字组成的。
- public static boolean equals(CharSequence a, CharSequence b)   判断两个字符串对象的内容是否相等，内部进行了非null判断
- public static String htmlEncode(String s)     将html代码中的特殊字符进行转码处理

3、android.webkit.URLUtil

虽然这个类叫`URLUtil`，但是其中封装了所有的判断是否是Android里`URI`的方法。比通过`String`的`startWith`方法判断更加严谨。
![](http://7xprht.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160116151107.png)

4、public static String format(String format, Object... args)

使用格式化字符串代替字符串的拼接，这样可以更好地对项目进行维护和国际化。比如`"距离您xx千米"`（xx保留两位小数）这样的字符串可以加上占位符即`"距离您%.2f千米"`，这样可以放在`res`文件中方便国际化，也可以减少因拼接字符串给代码带来的混乱。

常用的占位符有`%d`格式化整数,`%s`格式化字符串，`%f`格式化小数。更多关于format的信息可以查看[https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html](https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html "官方文档")

5、tools schemas

在新建Activity时IDE都会在xml的根节点默认加上tools的命名空间。

		<?xml version="1.0" encoding="utf-8"?>
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:app="http://schemas.android.com/apk/res-auto"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:orientation="vertical"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    app:layout_behavior="@string/appbar_scrolling_view_behavior"
		    tools:context="net.gzw1993.androidtips.DateUtilsActivity"
		    tools:showIn="@layout/activity_date_utils">
		
		    <Button
		        android:id="@+id/btn_select_date"
		        android:layout_width="match_parent"
		        android:layout_height="wrap_content"
		        tools:text="点击选择日期" />
		
		</LinearLayout>

这里介绍一种tools的简单用法。在写布局的时候经常会写一些无用的字符串用来预览布局，但是这些数据一般会在完成后删除，有时候在维护时为了方便预览再写上。这时可以使用`tools`的命名空间，比如`tools:text="点击选择日期"`tools仅仅可以预览，在运行的状态下解析布局文件时就会忽略过这条属性。



<div id="disqus_thread"></div>
<script>
/**
* RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
* LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL; // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');

s.src = '//gzw19931217.disqus.com/embed.js';

s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>

