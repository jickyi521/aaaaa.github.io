---
layout: post
title: "两种添加自定义view的方式"
date: 2014-06-14 15:17:13 +0800
comments: true
categories: iOS
---

这种使用场景最普遍了吧，稍不留神就碰到没有将自定的custom的view加成功的trick

`需要注意的两点`

 * 1、对于ViewController 在xib文件里 File's Owner 右侧 inspector 中的custom 对于ViewController,不然识别不了里面的outlet和IBAction

{% img /images/iOS_fileowner.png %}

* 2、对于普通的 custom UIView 需要将最顶层的UIView 的custom class填对应即可:

{% img /images/iOS_customclass.png %}

两种添加方式，上代码吧...

`第一种:`

 常见的方式在需要加入custom view处进行初始化
 需要注意的是，这种方式去初始化CustomView的时候是不会调 initFrame 构造方法，所以外面需要主动给frame

{% codeblock lang:objective-c %}
CustomView *customView = [[[NSBundle mainBundle] loadNibNamed:@"CustomView" owner:self options:nil] objectAtIndex:0];
{%endcodeblock %}


 `第二种:`

常见的方式在需要加入custom view处进行初始化, 然后在 CustomView 里面的  initWithFrame 去 load

{% codeblock lang:objective-c %}
CustomView *customView = [CustomView alloc] initWithFrame];

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        self = [[[NSBundle mainBundle] loadNibNamed:@"CustomView" owner:self options:nil] objectAtIndex:0];
    }
    return self;
}
 {%endcodeblock %}

推荐用第二种方式，封装性更好，也可以避免没有set frame的坑。