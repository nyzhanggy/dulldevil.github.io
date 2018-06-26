---
title: Mac 安装使用 Cycrip
categories: [聚沙成塔]
---


### 安装
到 [Cycrip官网](http://www.cycript.org)下载源文件，解压之后 cd 的对应的文件夹下，运行 cycript 文件

```
./cycript
```


如果报 ruby 版本的错误，可以下载对应的 ruby 库

1. brew install ruby@2.0
2. 成功后把/usr/local/Cellar/ruby@2.0/2.0.0-p648_2/lib/libruby.2.0.0.dylib拷贝到Cycript.lib目录下即可

## 使用

找到需要注入的进程

```
ps -e | grep appname
```

hook进程

```
./cycript -p  进程序号
```

进入 `cy#` 模式下就可以进行相应的操作了。
