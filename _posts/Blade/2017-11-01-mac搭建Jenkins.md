---
title: 搭建Jenkins
categories: [神兵利器]
---

## 安装
### 1.使用brew安装

```
brew install jenkins
```

### 2.链接 launchd 配置文件

```
ln -sfv /usr/local/opt/jenkins/*.plist ~/Library/LaunchAgents
```

如果要其他机器也可以访问，把ip地址改为广播地址:

```
<string>--httpListenAddress=0.0.0.0</string>
```

### 3.加/卸载配置

```
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.jenkins.plist

launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.jenkins.plist
```

### 4.启动Jenkins

```
jenkins
```

打开浏览器,输入 localhost:8080 就可看到Jenkins的web界面

### 5.Jenkins的一些操作

```
http://localhost:8080/exit       //退出Jenkins

http://localhost:8080/restart  //重启

http://localhost:8080/reload  //重新加载
```
## 遇到的问题
### 1.可选插件为空
插件管理的高级选项下 将升级站点的URL 换成 http://updates.jenkins.io/update-center.json
