---
title: WebKit 注入 JS
categories: [聚沙成塔]
---

webkit 中注入本地的js脚本，可以用下面的方法：

```objective-c
WKWebViewConfiguration* configuration = [[WKWebViewConfiguration alloc] init];

// 掺入脚本的时机 有 WKUserScriptInjectionTimeAtDocumentStart,WKUserScriptInjectionTimeAtDocumentEnd

WKUserScript *usrScript = [[WKUserScript alloc] initWithSource:@"console.log('insert js ')" injectionTime:WKUserScriptInjectionTimeAtDocumentStart forMainFrameOnly:YES];
    
[configuration.userContentController addUserScript:usrScript];

```
