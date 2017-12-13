---
title: 个人博客：GitHub Pages 
categories: [搭建个人博客]
---

### 开启GitHub Pages

1. 新建一个repository ，名字格式很重要，要像这样`<username>.github.io`；
2. 新建一个`index.html`文件，内容如下
    ```html
    Hello World
    ```
3. 保存提交

打开浏览器访问`<username>.github.io`，不出意外的话就能看到一句熟悉的问候。

### 配置 GitHub Pages
每一个仓库都会有`GitHub Pages`的配置信息，只不过当仓库名字为`<username>.github.io`，Github会自动设置`GitHub Pages`的信息。

接下来我们试着自己手动设置这些信息，新建一个名为`pageBlog`的仓库，它的`GitHub Pages`的配置信息信息是这样的

![GithubPages-unsettings](https://raw.githubusercontent.com/DullDevil/pics/master/person-blog/GithubPages-unsettings.png)


如果想要让这个仓库开启`GitHub Pages`的功能只需要修改下`Source`，选择为`master`分支，点击`save`。修改成功之后大致是这样的。

![GithubPagesSettings](https://raw.githubusercontent.com/DullDevil/pics/master/person-blog/GithubPages-settings.png)

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


### 设置风格

GitHub Pages 本身是有一些简单的[Jekyll Theme ](https://help.github.com/articles/creating-a-github-pages-site-with-the-jekyll-theme-chooser/)。在`Settings ->GitHub Pages`下点击`Choose a theme`，选择一个你喜欢的样式`Select Theme`，这里演示的就先选择`cayman`这个主题。

选完主题之后，回到你的仓库看一下，会发现多了一个`_config.yml`的文件，这个文件是一个Jekyll的配置文件，打开看一下里面只有一行代码，指定了一个`theme`。

```yml
theme: jekyll-theme-cayman
```

Jekyll 是一个转化工具，其实就是将markdown格式通过指定的模版转换成html页面，所以刚才创建的 index 和 wiki 页面的的格式需要修改。第一步要把后缀改为`.md`,然后把内容改为`markdown`语法。

修改完之后
index 页面
![index](https://raw.githubusercontent.com/DullDevil/pics/master/person-blog/list-index.png)



```yml
theme: jekyll-theme-cayman
title: MyBlog
description: This is My Blog
```
