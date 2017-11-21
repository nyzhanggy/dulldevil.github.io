---
title: Mac apache本地配置ssl证书 及 iOS OTA部署
categories: [持续集成]
---

如果要实现iOS的 OAT分发，现在必须通过https才能实现，于是自己闲来无事自己研究了一下。
首先第一步需要一个https的服务，Mac自带的apache可以很简单的是实现http的服务。对于https需要做的就是开启ssl功能就行了。
## 一、生成ssl证书
### 1、创建文件夹

为了文件的整洁，先创建一个文件夹来存放ssl相关的文件，手动创建或者命令行创建都可以。
路径也随便，只要找到就可以，不过一般会放在/private/etc/apache2/下，也建议放在这个文件目录下，便于管理。

```
sudo mkdir /private/etc/apache2/ssl
cd /private/etc/apache2/ssl
```

### 2、生成密钥对

通过openssl生成密钥对

```
sudo ssh-keygen -f server.key
```

### 3、生成证书请求文件

在设置信息的时候要注意 Common Name 一定要对应服务器地址

```
sudo openssl req -new -key server.key -out request.csr
```

![生成证书请求文件](http://upload-images.jianshu.io/upload_images/1681985-d10c3a65341a8280.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4、生成ssl证书（有效期为一年）

```
sudo openssl x509 -req -days 365 -in request.csr -signkey server.key -out server.crt
```


目前为止生成的ssl证书已经生成完毕，剩下的就是给apache配置ssl。




## 二、配置apache
配置ssl之前，先确保apache能够正常的访问使用
配置apache的文件是在一个隐藏的文件夹下，可以通过finder前往或者通过命令行直接打开
```
open /private/etc/apache2/
```

### 1、httpd.conf

打开 `/private/etc/apache2/httpd.conf ` 文件，查找以下配置，去掉前面的 "#"，

```
LoadModule ssl_module libexec/apache2/mod_ssl.so
Include /private/etc/apache2/extra/httpd-ssl.conf
Include/private/etc/apache2/extra/httpd-vhosts.conf
```


### 2、httpd-ssl.conf

打开 <code>/private/etc/apache2/extra/httpd-ssl.conf  </code> 文件
配置ssl证书和密钥的路径
<pre>SSLCertificateFile "/private/etc/apache2/ssl/server.crt"
SSLCertificateKeyFile "/private/etc/apache2/ssl/server.key"</pre>

### 3、httpd-vhosts.conf
打开 `/private/etc/apache2/extra/httpd-vhosts.conf` 文件
在文件的最后面添加以下信息
```
<VirtualHost *:443>
  SSLEngine on
  SSLCertificateFile /private/etc/apache2/ssl/server.crt
  SSLCertificateKeyFile /private/etc/apache2/ssl/server.key
  ServerName localhost
  DocumentRoot "这里填写文件的存储路径"
</VirtualHost>


**ServerName 服务地址 **
**DocumentRoot一般apache的文件都在/Library/WebServer/Documents/**
```



### 4、验证配置

```
sudo apachectl configtest
```

如果有以下错误

```
AH00526: Syntax error on line 92 of /private/etc/apache2/extra/httpd-ssl.conf:

SSLSessionCache: 'shmcb' session cache not supported (known names: ).
Maybe you need to load the appropriate socache module (mod_socache_shmcb?).
```

可根据提示进行修改，找到 `/private/etc/apache2/extra/httpd-ssl.conf` 的92行。

去掉对应行的注释
```
LoadModule socache_shmcb_module modules/mod_socache_shmcb.so 
```

### 5、启动或重启apache
```
sudo apachectl restart
```

现在就可以通过https访问apache服务了，不过由于是自签名的会有安全警告，手动设置信任就行了。


## 三、OTA部署

在打包的过程中，到下面这步的时候，如果勾选下图的选择框会引导生成OTA所需要的plist文件

![勾选选择框](http://upload-images.jianshu.io/upload_images/1681985-2068bb941217c235.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击next之后会出现如下界面，进行相关信息的填写，下面的四项都是必填项

![填写信息](http://upload-images.jianshu.io/upload_images/1681985-2378d05e1866f1e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于图片有的可能会不需要，并且在plist 文件中没有图片也不影响功能的实现，所以随便就可以，这些信息后期都可以通过修改plist文件来改变。

利用Xcode自动生成的好处就是省去了自己构建plist结构的过程，如果你已经有现成的plist文件就不用勾选第一幅图的选择框，只用修改相应的内容就可以了。

现在我们已经有了https的服务器、ipa安装包和plist文件，OTA所需要的东西已经齐全，剩下的就是将ipa和plist文件方法到服务端，提供下载安装。

![plist 文件内容](http://upload-images.jianshu.io/upload_images/1681985-8003dd4e61958183.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

apache服务的默认文件位置在<code>/Library/WebServer/Documents/</code>，可以创建一个文件来放置OTA所需的文件

![文件目录](http://upload-images.jianshu.io/upload_images/1681985-4d33b715cb17daff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

index.html的内容，两个连接，一个下载安装包，一个现在ssl的证书
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" mime-types="text/plain">
    <title></title>
  </head>
  <body>
    <a classstyle="display:block" href="itms-services://?action=download-manifest&url=https://192.168.0.129/OTA/manifest.plist">下载ipa</a>
    <a style="display:block" href="https://192.168.0.129/OTA/ssl/server.crt">下载证书</a>
  </body>
</html>

```

点击"下载ipa"，不出意外的话就会出现下面的界面，点击安装就ok了。

![安装提示](http://upload-images.jianshu.io/upload_images/1681985-06ba89fe7269c397.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)

<h2>总结一下遇到的坑</h2>
最大的坑就是在生成证书请求文件时，没注意到 Common Name 的设置，以至于后面点击下载的时候一直提示连接失败，找了好久才找到这个问题。

---
