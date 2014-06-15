---
layout: post
title: "遍历发消息"
date: 2014-06-14 13:07:07 +0800
comments: true
categories: iOS
---

##应用场景##

对一个有许多 subview 的 ViewContainer 要加入新view之前需要先移干净 之前的view，以防止新view被污染
以前的做法

{% codeblock lang:objective-c %}

for(UIView *subview in self.view.subviews){
        [subview removeFromSuperview];
 }

{% endcodeblock%}

 
`发现一种新的写法`

这种方式对遍历封装好了，简洁且高效
//Sends to each object in the array the message identified by a given selector, starting with the first object and continuing through the array to the last object.

{% codeblock lang:objective-c %}

	[self.view.subviews makeObjectsPerformSelector:@selector(removeFromSuperview)];
{% endcodeblock%}