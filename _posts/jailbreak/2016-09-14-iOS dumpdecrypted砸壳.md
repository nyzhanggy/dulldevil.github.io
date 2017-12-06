---
title: iOS 逆向： dumpdecrypted砸壳
categories: [逆向工程]
---


> 这篇文章以目标为： 得到解密的二进制文件

### 1.准备工作

首先要有一台越狱设备，然后安装插件：OpenSSH,[Cycript](http://www.cycript.org/)。
然后下载[dumpdecrypted](https://github.com/stefanesser/dumpdecrypted/archive/master.zip)并解压，cd 到文件所在位置，执行```make```命令，会生成一个```dumpdecrypted.dylib```文件这就是我们等下砸壳所要用到的砸壳工具。

现在需要的东西已经准备好了，接下来可以开始砸壳了！
### 2.开始砸壳
通过ssh连接到设备，默认的密码是：```alpine```

```
ssh root@设备的ip地址
```

运行你要砸壳的应用，通过命令你查看进程，执行命令之后会显示当前运行的所有进程。找到想要砸壳的应用进程，记录一下路径和编号。

```
ps -e       
```

![ps -e](http://upload-images.jianshu.io/upload_images/1681985-d0191629cdfe928d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/520)

再执行命令进入cycript，执行之后命令行会以```cy#```开头

```
cycript -p 进程编号

```
执行命令，获取沙盒路径

```
[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask][0]
```

![获取沙盒路径](http://upload-images.jianshu.io/upload_images/1681985-408300cd263ca113.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/520)

回去之后，我们可以通过 ctrl＋d 退出cycript。以便接下来的操作。

现在我们得到了两个路径，一个app的沙盒路径，一个进程的运行路径。接下来我们需要将砸壳的工具放到app的沙盒下。
可以通过scp命令进行远程copy，由于当前的这个命令行还要用，所以用快捷键```command＋n``` 再开启一个命令行窗口。用scp命令将```dumpdecrypted.dylib```文件复制到到设备中。

```
scp  dumpdecrypted路径  root@设备的ip地址:App沙盒路径
```

执行之后会让输入密码，输入密码开始上传。
上传成功之后，回到链接手机的命令行，cd到App的沙盒下

```
cd app沙盒路径
```

先执行下面的命令，防止遇到```killed 9```的错误

```
su mobile
```

可以查看下是否有```dumpdecrypted.dylib```文件，如果有的话就执行砸壳命令

```
DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib  App进程路径
```

命令执行完成之后会得到```.decrypted```文件，这个就是砸壳后的文件。

同样通过scp命令将脱壳之后的二进制文件复制到电脑上

关于插件的破解，先要理解[dumpdecrypted ](https://github.com/stefanesser/dumpdecrypted)的原理。它的原理是：将应用程序运行起来（iOS系统会先解密程序再启动），然后将内存中的解密结果dump写入文件中，得到一个新的可执行程序。 想要破解哪个应用就在```DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib App进程路径```把进程对应的路径填写进去就行了。
虽然可以破解，但是我做的时候结果还是用不了 ，控制台的输出为 "插件无效"，况且这个插件也不常用就懒得弄。

## 参考链接：
 [iOS逆向工程(简单利用"dumpdecrypted"给ipa砸壳)](http://www.jianshu.com/p/a4373b5feca0)
[App Extension的脱壳](https://nianxi.net/ios/dump-decrypted-ios-app-extensions.html)
