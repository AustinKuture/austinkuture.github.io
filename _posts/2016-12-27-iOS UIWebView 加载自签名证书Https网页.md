---
layout: post
title: iOS UIWebView 加载自签名证书Https网页
categories: iOS
description: iOS UIWebView 加载自签名证书Https网页
keywords: iOS,https,自签名证书,UIWebView
comments: true
---




# iOS UIWebView 加载自签名证书Https网页 {#toc_0}

作者:**AustinKuture**

```
摘要: 在上一篇文章中主要讲解了,如何使用AFN验证自签名证书并加载Https资源数据.
     但是对于自签名证书的网页却不能正常加载,本篇文章讲解如何在加载自签名证
     书时自动信任证书并加载网页,后续会将如何进行自签名证书的双向认证并加载.
```

* 目前源码已经上传至Githup需要参考源码的可以点击这里:[https://github.com/AustinKuture/UIWebViewSelfSignedHttps.git](https://github.com/AustinKuture/UIWebViewSelfSignedHttps.git)

* 使用UIWebView 加载页面时,我们的常用方法大都是这样的\(或者使用WKWebView\):

  ```
  UIWebView *webView = [UIWebView alloc]initWithFrame:CGRectMake(0,0,200,200);
  [webView loadRequest:[NSURLRequest requestWithURL:url]];
  [self.view addSubView:webView];
  ```

* 有时我们也会用UIWe不View加载网页时设置Cookie,在这里我们使用NSHTTPCookieStorage实现管理共享的cookie,并将存储一个共享实例,这些Cookie在所有应用程序之间是共享的,并且是跨进程同步保存的,通过对Cookie设置可以让我们像电脑浏览网页一样,灵活的对Cookie信息进行设置和清除,使应用端加载网页信息时更加的方便,Cookie的设置与清除如下所示:

  ```
  //设置Cookie
  NSMutableDictionary *cookieProperties = [NSMutableDictionary dictionary];  
  [cookieProperties setObject:@"cookie_user" forKey:NSHTTPCookieName];  
  [cookieProperties setObject:uid forKey:NSHTTPCookieValue];  
  [cookieProperties setObject:@"xxx.xxx.com" forKey:NSHTTPCookieDomain];
  [cookieProperties setObject:@"/" forKey:NSHTTPCookiePath];  
  [cookieProperties setObject:@"0" forKey:NSHTTPCookieVersion]; 
  [cookieProperties setObject:[[NSDate date] dateByAddingTimeInterval:2629743] forKey:NSHTTPCookieExpires];  

  NSHTTPCookie *cookieuser = [NSHTTPCookie cookieProperties];  
  [[NSHTTPCookieStorage sharedHTTPCookieStorage] setCookie:cookieuser]; 

   NSURL *url = [NSURL URLWithString:url];   
   NSURLRequest *request = [NSURLRequest requestWithURL:url cachePolicy:NSURLRequestReturnCacheDataElseLoad timeoutInterval:60];  
   [webView loadRequest:request];

  //清除Cookie
  - (void)deleteCookie{  
      NSHTTPCookie *cookie;  

      NSHTTPCookieStorage *cookieJar = [NSHTTPCookieStorage sharedHTTPCookieStorage];  

      NSArray *cookieAry = [cookieJar cookiesForURL: [NSURL URLWithString: url]];  

      for (cookie in cookieAry) {  

          [cookieJar deleteCookie: cookie];  

      }  
  }
  ```

* 虽然UIWebView使用较为灵活,几乎与电脑端加载网页一样的方便,但是由于种种原因iOS的UIWebView在使用自签名证书加载网页时仍然不能正常的加载数据并显示,对此我们可以使用NSURLConnection和UIWebView的代理方法,实现对自签名证书在一定情况下,自动在程序后台进行验证与自动信任,从而加载出我们所想要显示的网页,具体使用步骤如下:

### 1,创建一个继承自UIWebView的类AKWebView,在.h文件中添加两个方法,一个是初始化类方法\(单例\),另一个是WebView的实现方法: {#toc_1}

```
    #import 
<
UIKit/UIKit.h
>


    @interface AKWebView : UIWebView

    +(instancetype)shareWebView;
    - (void)webViewWithLoadRequestWithURL:(NSURL *)url Fram:(CGRect)fram;

    @end
```

### 2,在.m文件中,首先添加我们所要用到的代理协议,并创建属性: {#toc_2}

```
    #import "AKWebView.h"

    @interface AKWebView ()
<
UIWebViewDelegate,NSURLConnectionDataDelegate
>


    @property (nonatomic,strong) NSURLConnection *urlConnection;
    @property (nonatomic,strong) NSURLRequest *requestW;
    @property (nonatomic) SSLAuthenticate authenticated;

@end
```

### 3,实现类初始化方法shareWebView: {#toc_3}

```
@implementation AKWebView

+(instancetype)shareWebView{
    static dispatch_once_t onece = 0;
    static AKWebView *webView = nil;
    dispatch_once(
&
onece, ^(void){
        webView = [[self alloc]init];
    });
    return webView;
}
```

### 4,实现webView的实例化方法,在这里主要是为了配置WebVeiw的大小,布局,添加代理,以及Request: {#toc_4}

```
#pragma mark ***UIWebView 加载方法***
- (void)webViewWithLoadRequestWithURL:(NSURL *)url Fram:(CGRect)fram{

    self.frame = fram;
    self.delegate = self;
    _requestW = [NSURLRequest requestWithURL:url];
    [self loadRequest:_requestW];
}
```

### 5,实现各代理方法: {#toc_5}

```
#pragma mark ***UIWebView 代理方法***
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType{

    NSLog(@"开始加载: %@ 授权:%d", [[request URL] absoluteString], _authenticated);

    if (!_authenticated) {
        _authenticated = kNeverAuthenticate;

        _urlConnection = [[NSURLConnection alloc] initWithRequest:_requestW delegate:self];

        [_urlConnection start];

        return NO;
    }

    return YES;
}


#pragma mark ***NURLConnection 代理方法***
- (void)connection:(NSURLConnection *)connection didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge{
    NSLog(@"WebController 已经得到授权正在请求 NSURLConnection");

    if ([challenge previousFailureCount] == 0){
        _authenticated = kTryAuthenticate;

        NSURLCredential *credential = [NSURLCredential credentialForTrust:challenge.protectionSpace.serverTrust];

        [challenge.sender useCredential:credential forAuthenticationChallenge:challenge];

    } else{
        [[challenge sender] cancelAuthenticationChallenge:challenge];
    }
}

- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response{
    NSLog(@"WebController 已经收到响应并通过了 NSURLConnection请求");

    _authenticated = kTryAuthenticate;
    [self loadRequest:_requestW];
    [_urlConnection cancel];
}

- (BOOL)connection:(NSURLConnection *)connection canAuthenticateAgainstProtectionSpace:(NSURLProtectionSpace *)protectionSpace{

    return [protectionSpace.authenticationMethod isEqualToString:NSURLAuthenticationMethodServerTrust];
}


@end
```

### 6,使用方法,在使用时候先导入AKWebView的头文件,在想要加载自签名证书https网页的控制器中,创建AKwebView并实现其方法,添加URL,及Fram大小即可: {#toc_6}

```
AKWebView *webView = [AKWebView shareWebView];
[webView webViewWithLoadRequestWithURL:url Fram:CGRectMake(0, 0, 500, 500)];
[self.view addSubview:webView];
```



