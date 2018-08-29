---
title: WebKit 注入 JS
categories: [聚沙成塔]
---

webkit 中注入本地的js脚本，可以用下面的方法：

```objective-c
NSString *jsStr = @"";
[_wkWebView evaluateJavaScript:jsStr completionHandler:^(id _Nullable res, NSError * _Nullable error) {
    NSLog(@"%@",error);
}];
```

关于注入脚本的位置，可以根据实际情况选择。一般在 主页面开始加载 的时候进行注入比较合理，也能解决reload造成注入的脚本消失的问题。

```objective-c

//在发送请求之前，决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler;

//在收到响应后，决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationResponse:(WKNavigationResponse *)navigationResponse decisionHandler:(void (^)(WKNavigationResponsePolicy))decisionHandler;

//主页面开始加载
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(null_unspecified WKNavigation *)navigation;

//主页面开始接收到数据
- (void)webView:(WKWebView *)webView didCommitNavigation:(null_unspecified WKNavigation *)navigation;

//主页面数据加载完成
- (void)webView:(WKWebView *)webView didFinishNavigation:(null_unspecified WKNavigation *)navigation;
```
