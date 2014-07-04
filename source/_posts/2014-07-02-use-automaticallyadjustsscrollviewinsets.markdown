---
layout: post
title: " AutomaticallyAdjustsScrollViewInsets 和 Translucent 属性"
date: 2014-07-02 22:18:15 +0800
comments: true
categories: iOS
---

最近在调UITabBarVC 里navigationbar吃掉contentview bug，最后主要定位到 automaticallyAdjustsScrollViewInsets 和 translucent属性使用不正确


iOS 7.0 statusbar 20points 和 navigationbar 44points在默认的情况下是透明的也就是VC的
self.view （0，0）是从 最左上角开始。

{% img /images/0702-01.png %} 

在默认情况下普通的UIView和UIScrollView添加 红色的view (0,0,64,64) 展现的形式是不一样的

{% codeblock lang:objective-c %}
-(void)addRedUIView{
    UIView *redView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 64, 64)];
    redView.backgroundColor = [UIColor redColor];
    [self.view addSubview:redView];
}
{% endcodeblock %}

* UIVIew

{% img /images/0702-02.png %} 

* UITableView

{% img /images/0702-03.png %} 

这种情形由 automaticallyAdjustsScrollViewInsets 和 translucent属性所决定

##automaticallyAdjustsScrollViewInsets##

`Specifies whether or not the view controller should automatically adjust its scroll view insets.
Default value is YES, which allows the view controller to adjust its scroll view insets in response to the screen areas consumed by the status bar, navigation bar, and toolbar or tab bar. Set to NO if you want to manage scroll view inset adjustments yourself, such as when there is more than one scroll view in the view hierarchy.`

这个参数是7.0 出来，主要是为带有 UIScrollView 的控件所服务，且属性默认是YES, 也就是说
<!--more-->在透明bar情形下，都会给加上个 64points 的insets， insets就是一个padding概念。
[self.tableView setContentInset:UIEdgeInsetsMake(64, 0, 0, 0)];


* 1、navigationbar 透明

{% img /images/07-03-01.png %}


即 self.navigationController.navigationBar.translucent = YES; 往navigation里面加的view都会往navigationbar里面进去，
对于scrollview的控件可以加个 UIEdgeInsetsMake(64, 0, 0, 0) 的insets，

而对于普通的uiview这种情况推荐最上层套个UIScrollView去处理，也可以将view在IB里面往下移动64去显示，注意点对对齐位置方式。不建议用代码去控制，太繁琐，尤其在考虑多屏幕下横竖屏情况，用autolayout是最合适的方式，还不熟悉呢这块。   


* 2、navigationbar 不透明

view自动在navigationbar下面显示，不需要处理。

 
 [iOS 6 - 7 的status bar兼容](http://www.doubleencore.com/2013/12/reconciling-ios-6-ios-7-using-interface-builder/)   <br>
 [Developer’s Guide to the iOS 7 Status Bar](http://www.doubleencore.com/2013/09/developers-guide-to-the-ios-7-status-bar/)

