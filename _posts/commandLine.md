

# 命令行

[TOC]

#### pods
安装Cocoapods

```
sudo gem install cocoapods
```

初始化

```
pod setup

```

更新cocoapods版本时出现

```
xuanxuandeMacBook-Pro:Specs xuanxuan$ sudo gem update cocoapods  
Updating installed gems
Updating cocoapods
ERROR:  While executing gem ... (Errno::EPERM)
    Operation not permitted - /usr/bin/xcodeproj

```

用下面的命令

```
http://stackoverflow.com/questions/30812777/cannot-install-cocoa-pods-after-uninstalling-results-in-error/30851030#30851030
sudo gem install -n /usr/local/bin cocoapods --pre
```

查看本地的pods仓库

```
pod repo

```

更新pods仓库

```
pod repo update master
```

pod install速度慢的终极解决方案

git设置全局代理

```
git config --global http.proxy socks5://127.0.0.1:1080
```

只需要github上的代码库走代理

```
git config --global http.https://github.com.proxy socks5://127.0.0.1:1080
```

如果要恢复/移除上面设置的git代理，使用如下命令

```
git config --global --unset http.proxy
git config --global --unset http.https://github.com.proxy
```

#### 私有pods仓库
创建podspec

```
pod spec create
```

验证spec文件有效性

```
pod spec lint .podspec
```

添加私有repo到CocoaPods中

```
pod repo add 'REPO_NAME' 'SOURCE_URL'
```

验证私有repo安装无误

```
cd ~/.cocoapods/repos/REPO_NAME
pod repo lint .

```



#### 逆向工程
检验app是否加密

```
otool -l /Users/limingwei/Desktop/wechat/WeChat | grep crypt

cryptoff 16384
cryptsize 45678592
cryptid 0
```

导出头文件

```
class-dump -H /Users/limingwei/Desktop/resign/WeChat.decrypted -o /Users/limingwei/Desktop/resign/classdump 
```


lipo 分离/合并 ARMV7、ARM64架构dylib

```
分离
lipo -thin armv7 xxxx.dylib -output xxxx_v7.dylib
lipo -thin arm64  xxxx.dylib -output xxxx_64.dylib
合并
lipo -create ./xxxx_v7.dylib ./xxxx_64.dylib -output ./xxxx.dylib
```


查看依赖库

```
otool -L /Users/limingwei/Desktop/wechat/WeChat.dylib
```

动态注入

```
install_name_tool -add_rpath @loader_path/libsunstrate.dylib /Users/limingwei/Desktop/wechat/WeChat.dylib
```

重签名

```
./ios_resign_from_app_to_ipa app-extracted "iPhone Distribution: Hangzhou Jing CAI Network Technology Co., Ltd" embedded.mobileprovision resign.ipa
```

生成Tweak.xm

```
logify.pl /path/to/BaseMsgContentViewController.h > /out/to/Tweak.xm
```
#### 模拟器

启动运行模拟器：

```
xcrun instruments -w 'iPhone 6 Plus'
```

在已经启动好的模拟器中安装应用：（*这里要特别注意，是app，不是ipa* 安装时需要提供的是APP的文件路径）

```
xcrun simctl install booted Calculator.app
```

卸载APP的命令就比较简单了：

```
xcrun simctl uninstall booted com.yuchang.calculator
```

#### 权限

mac sierra 未知来源软件

```
sudo spctl --master-disable
```

添加读写权限

```
chmod 777
```

#### sshkey
生成sshkey

```
ssh-keygen -t rsa -C "2239991713@qq.com"

```

#### plist文件
mac 命令行查看plist

```
/usr/libexec/PlistBuddy -c Print Inhouse-Info.plist
```

#### 设置别名

```
alias mysql=/usr/local/mysql/bin/mysql

```

#### 自动化code review

```
 brew tap oclint/formulae
 brew install oclint
 gem install xcpretty
```

####  打包脚本
xcodebuild 添加 SYMROOT=buildDir 设置bulid路径

#### 自动填充

```
#!/usr/bin/expect

set timeout 1200
set password izhuan123QWEdsa
set downloadFileName [lindex $argv 0]
set localPath [lindex $argv 1]
spawn scp root@120.26.106.167:/usr/local/tomcats/tomcat8-80-ssl/webapps/app/qualitydev/${downloadFileName} $localPath
expect {
  # 第一次使用scp时 会提示授权
    "yes/no" { send "yes\r"; exp_continue}
    "password:" { send "$password\r" }
}
expect eof
```



[!] Unable to find a pod with name, author, summary, or description matching `openssl`

删除cocoapods的索引，然后重新search，

rm ~/Library/Caches/CocoaPods/search_index.json

pod search AFNetworking





add $(PLATFORM_DIR)/Developer/Library/Frameworks into Framework Search Paths


## 命令行参数

编辑 ~/.bash_profile

gxfxaxdxhxegedabagacad

CLICOLOR是用来设置是否进行颜色的显示。CLI是Command Line Interface的缩写。
LSCOLORS是用来设置当CLICOLOR被启用后，各种文件类型的颜色。LSCOLORS的值中每两个字母为一组，分别设置某个文件类型的文字颜色和背景颜色。LSCOLORS中一共11组颜色设置，按照先后顺序，分别对以下的文件类型进行设置：
directory
symbolic link
socket
pipe
executable
block special
character special
executable with setuid bit set
executable with setgid bit set
directory writable to others, with sticky bit
directory writable to others, without sticky bit
LSCOLORS中，字母代表的颜色如下：

a 黑色
b 红色
c 绿色
d 棕色
e 蓝色
f 洋红色
g 青色
h 浅灰色
A 黑色粗体
B 红色粗体
C 绿色粗体
D 棕色粗体
E 蓝色粗体
F 洋红色粗体
G 青色粗体
H 浅灰色粗体
x 系统默认颜色
