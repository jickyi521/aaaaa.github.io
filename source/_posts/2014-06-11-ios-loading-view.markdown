---
layout: post
title: "iOS-Loading-View"
date: 2014-06-11 22:42:44 +0800
comments: true
categories: iOS
---

##应用场景##

	在对用户或者流程上需要进行堵塞操作时候需要block，尤其是像Login, Oath 这种网络请求，
	这时候就需要个loadingview来作为缓冲（对于些展示的界面的网络请求，没有必要block)。
	对全局写个易用性好的LoadingView就很有必要，这个View相当于是个资源的Manager，基于这些写个单例是非常适合此种情况的。

·上代码吧....·

{% codeblock lang:objective-c %}

.h
+(id)getShareInstance;
-(void)show;
-(void)stop;

.m
+(id)getShareInstance{
    
    if(!instance){
        instance = [[JYTLoadingView alloc] init];
    }
    return instance;
}


- (id)initWithFrame:(CGRect)frame
{
    if (self) {
        self = [[[NSBundle mainBundle] loadNibNamed:@"JYTLoadingView" owner:self options:nil] objectAtIndex:0];
        
        CGRect frame = [UIScreen mainScreen].bounds;
        self.center = CGPointMake(frame.size.width/2, frame.size.height/2);
        self.layer.cornerRadius = 8.0;
    }
    return self;
}

-(void)show{
    [self.indicatorView startAnimating];
    [[UIApplication sharedApplication].keyWindow addSubview:self];
     timer = [NSTimer scheduledTimerWithTimeInterval:3 target:self selector:@selector(stop) userInfo:nil repeats:NO];
    
}

-(void)stop{
    [self removeFromSuperview];
}

{% endcodeblock %}

<!--more-->

`V2改进版，对getShareInstance进一步封装，对与外围试用者而言，只需要知道show和stop接口即可:`

{% codeblock lang:objective-c %}

.h
-(void)show;
-(void)stop;

.m
+(JYTLoadingView *) getShareInstance{
    if(!instance){
        instance = [[JYTLoadingView alloc] init];
    }
    return instance;
}

- (id)initWithFrame:(CGRect)frame
{
    if (self) {
        self = [[[NSBundle mainBundle] loadNibNamed:@"JYTLoadingView" owner:self options:nil] objectAtIndex:0];
        
        CGRect frame = [UIScreen mainScreen].bounds;
        self.center = CGPointMake(frame.size.width/2, frame.size.height/2);
        self.layer.cornerRadius = 8.0;
    }
    return self;
}

+(void)show{
    
    [[[JYTLoadingView getShareInstance] indicatorView] startAnimating];
    [[UIApplication sharedApplication].keyWindow addSubview: [JYTLoadingView getShareInstance]];
     timer = [NSTimer scheduledTimerWithTimeInterval:3 target:self selector:@selector(stop) userInfo:nil repeats:NO];
}

+(void)stop{
    [[self getShareInstance] removeFromSuperview];
}


{% endcodeblock %}


`V3 .m进行改进，对于代码alloc 的方式，view走 initWithFrame, 而对于通过 IB方式的设计的view， 是不会走这个生命周期中的方法`


* iOS文档：
If you create a view object programmatically, this method is the designated initializer for theUIView class. Subclasses can override this method to perform any custom initialization but must callsuper at the beginning of their implementation.

If you use Interface Builder to design your interface, this method is not called when your view objects are subsequently loaded from the nib file.

{% codeblock lang:objective-c %}
+(LoadingScreen *)getShareInstance{
    if(!shareInstance){
        shareInstance = [[[NSBundle mainBundle] loadNibNamed:@"LoadingScreen" owner:self options:nil] objectAtIndex:0];
        [shareInstance mainLoadingView].center = shareInstance.center;
        [shareInstance loadingLable].text = NSLocalizedString(@"Indicator.Message.Loading", nil);
    }
    return shareInstance;
}

+(void)show{
    [[[LoadingScreen getShareInstance] indicatorView] startAnimating];
    [[UIApplication sharedApplication].keyWindow addSubview:[LoadingScreen getShareInstance]];
}

+(void)stop{
    [[[LoadingScreen getShareInstance] indicatorView] stopAnimating];
    [[LoadingScreen getShareInstance] removeFromSuperview];
}

{% endcodeblock %}

