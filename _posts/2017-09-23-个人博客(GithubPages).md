---
title: 搭建个人博客（二）
categories: [笔记]
---

### 开启GitHub Pages
上一篇说到，创建一个名为`<username>.github.io`的仓库之后，就能通过`<username>.github.io`访问到我们的博客，这是因为在仓库的`GitHub Pages`配置。

每一个仓库都会有`GitHub Pages`的配置信息，只不过当仓库名字为`<username>.github.io`，Github会自动设置`GitHub Pages`的信息。

接下来我们试着自己手动设置这些信息，新建一个名为`pageBlog`的仓库，它的`GitHub Pages`的配置信息信息是这样的

![GithubPages-unsettings](/assets/images/person-blog/GithubPages-unsettings.png)


如果想要让这个仓库开启`GitHub Pages`的功能只需要修改下`Source`，选择为`master`分支，点击`save`。修改成功之后大致是这样的。

![GithubPagesSettings](/assets/images/person-blog/GithubPages-settings.png)

现在可以看到 在上面多了一句说明
```
Your site is ready to be published at https://<username>.github.io/pageBlog/.
```

这个时候就可以通过`https://<username>.github.io/pageBlog/`访问了，**不过要保证你的仓库里要有一个`index.html`文件。**



### 设置域名
在下面还有一个选项`Custom domain`，这个是设置域名的。如果你手上正好有一个域名，那就可以用起来了。
在输入框填上你的域名，点击`save`。现在回到仓库看一眼，发现多了一个`CNAME`的文件，这个文件中只有一行信息，就是你的域名。

接下来到你的域名管理平台上，以阿里云为例，添加域名解析。**注意记录值最后面还有个`.`**

|名称|参数|
|:-:|:-:|
|记录类型|CNAME|
|主机记录|@|
|记录值| `<username>.github.io.`|
|解析线路| 默认|
|TTL|      10分钟|

设置完成之后就可以通过你的域名访问你的博客了。如果还想更加深入的了解`Github Pages`，可以看[官方的介绍](https://pages.github.com/)。
