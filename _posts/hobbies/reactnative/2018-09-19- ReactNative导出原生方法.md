---
title: ReactNative导出原生方法
categories: [略知一二]
---

在 ReactNative 与 iOS 原生交互的时候，需要在 iOS 端提前定义相应的方法。这是就需要用到一个宏`RCT_REMAP_METHOD`，宏的定义如下：


```objective-c
#define RCT_REMAP_METHOD(js_name, method) \
  _RCT_EXTERN_REMAP_METHOD(js_name, method, NO) \
  - (void)method RCT_DYNAMIC;
```

这里又使用到了另一个宏 `RCT_REMAP_METHOD`，并且生成了一个实例方法。

宏`RCT_REMAP_METHOD`定义如下：

```objective-c
#define _RCT_EXTERN_REMAP_METHOD(js_name, method, is_blocking_synchronous_method) \
  + (const RCTMethodInfo *)RCT_CONCAT(__rct_export__, RCT_CONCAT(js_name, RCT_CONCAT(__LINE__, __COUNTER__))) { \
    static RCTMethodInfo config = {#js_name, #method, is_blocking_synchronous_method}; \
    return &config; \
  }
```

这里涉及到一个类方法，类方法的方法名有也是通过宏生成的。这里用的宏`RCT_CONCAT`的定义如下：

```objective-c
#define RCT_CONCAT(A, B) RCT_CONCAT2(A, B)
```

`RCT_CONCAT` 这个宏本质是 `RCT_CONCAT2`，定义如下：

```objective-c
#define RCT_CONCAT2(A, B) A ## B
```

`RCT_CONCAT2` 的作用就是将传入的 A、B 连接在一起。也就意味着 `RCT_CONCAT` 的作用也是将传入的内容进行拼接。

现在回过头来看上面的类方法名是如何生成的。

>`__LINE__`:展开该宏时在文件中的行数  
`__COUNTER__`:无重复的计数器，从程序启动开始每次调用都会++

如果js_name为test，method 为 `test(){NSLog(@"Test")}` 所在行为26，第一次使用 `RCT_REMAP_METHOD` ,生成的方法如下：

```objective-c
+ (const RCTMethodInfo *)__rct_export__test260{
    static RCTMethodInfo config = {"test","test{NSLog(@\"Test\")}",NO};
    return &config;
}

- (void)test {
    NSLog(@"Test");
}
 ```



