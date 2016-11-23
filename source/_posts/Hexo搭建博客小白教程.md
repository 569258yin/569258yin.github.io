---
title: Hexo搭建博客小白教程
date: 2016-11-22 07:44:12
categories: Hexo
tags: hexo
---

### 前言·
利用hexo在Github上搭建个人博客是我从我对象那里知道的，她学的前端，我学的Java，一直没了解过这个东西，前几天发现了，果断喜欢上了，以前都是在CSDN上写博客。
然后，我就开始了研究，到了现在，终于好了，网上教程确实很多啊，但是其间也有一些问题，然后昨天，我女朋友问了几个问题(女友还是小白啊，毕竟妹子)，在添加主题的地方。
比如：
```

git clone http:.......git themes/icuals
cd themes/icuals
git pull

```
这样我们觉得很简单的，对于刚学的妹子来说确实是一个挑战啊！！对于Lunix命令基本不懂得啊.So，我就写了这篇博客给我女友看，是不是很有爱的啊。

### 参考
先放我看过的教程，都可以提供参考的。
[使用hexo，如果换了电脑怎么更新博客？](https://www.zhihu.com/question/21193762)
[hexo+github快速搭建个人博客](https://jayzangwill.github.io/blog/2016/07/31/hexo-github%E5%BF%AB%E9%80%9F%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/#more)

### Github项目创建

* 1.注册Github账号
[Github官网](https://github.com/)
{% asset_img github_sign_up.png [注册账号] %}
点击上图的** sign up ** 
然后就进入下图填写个人注册信息的界面，上面还有总步骤说明
{% asset_img github_signup_info.png [注册账号详情] %}
然后把三个步骤走完就OK啦！这时候你就拥有了你的Github账号和远程仓库。

* 2.创建项目
在你登录过的Github界面，右上方找到一个 **+** 号，然后点击，再选择new repository,就是新建项目啦。
{% asset_img 20161122214120.png [创建项目引导] %}
然后就是新建项目界面，输入你要新建的项目名称，这里我们要建一个博客，所有名字要为你的Github账号加上**.github.io**
比如我的Github账号为**569258yin**，那我创建的项目名称就为**569258yin.github.io**,下图可以看到我的项目名称已经存在了。
{% asset_img 20161122214451.png [] %}
然后描述内容可以不写，或者写...（*对了，免费版的Github账号都是public的，也就是你的项目被人都可以看到，但是别人是没有权利修改的*）

* 3.创建分支

        创建分支为了我们能够在不同的电脑上方便修改博客，首先，Github上master主分支存放
        的是hexo将我们的源文件转化生成的静态网页，是不存储我们实际操作的源文件的，我刚开
        始也不知道，然后去了公司，从Github上克隆了我的项目，准备做修改呢，结果全是静态网
        页，并不能直接修改，所有参考了知乎上大神给的方法。
        
    ** 项目分为两个分支：master主分支用了存放静态网页，hexo(名字随意)分支用了存放项目源文件 **

当刚创好分支，如果你还没添加任何描述，项目界面是这样的
{% asset_img 20161123075138 [] %}
如果你的也是，就点图中红色标注的**README**，然后就可以添加一些描述啦。这里可以简单写一句话，关于如何使用README，可另行百度。
如果你的界面是下面的这种，那就可以直接点**Branch:master**,(*branch的意思就是分支,master代表主分支*)，在文本框中输入hexo（分支名）
然后点击create branch:hexo就可以了。
{% asset_img 20161123080021.png []%}

* 4.设置hexo分支为默认分支
点击项目设置,进到如下界面
{% asset_img 20161123205751.png [] %}
然后再选择Branch设置，可以看到当前默认分支为master，点击选择我们创建的hexo，然后点击右边update就可以了
{% asset_img 20161123205923.png []%}


### 本地搭建Hexo博客
* 1.在本地创建一个文件夹，比如我的取名为hexo，然后鼠标右键git branch，调出git控制台工具。如果你还没有装过这个软件，
那就需要先安装[软件下载地址](https://git-for-windows.github.io),不会安装的同学可以参考
[廖雪峰的Git安装教程及使用说明](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000)
打开控制台后输入

`
git clone https://github.com/569258yin/test.github.io.git
`

克隆你在Github创建的项目到本地，后面的网址是我的，你的项目网址在Github网站项目可以查看到，如下图：
{% asset_img 20161123210805.png [] %}
克隆完成后就会在hexo生成对应的项目文件夹
{% asset_img 20161123210936.png [] %}

* 2.安装Node.js

下载地址：[Node官网](https://nodejs.org/en/)，下载任意版本，安装就可以了，这个很简单。安装完成可以在git bash控制台输入 **node -v ** 如果能显示版本号就说明安装成功！

* 3.安装hexo插件

在git bash中利用node 自带的包管理器进行安装：

```
输入 npm install -g hexo(或者npm i -g hexo)
```

* 4.初始化hexo

首先需要将git bash 当前文件路径选到你的项目根目录下，不会命令的可以直接将当前的git bash关掉，然后直接鼠标点进去项目文件夹，鼠标右键选择 git bash 就可以了
然后执行

```
hexo init
```

这一步，我只能说超级慢的啊，视网络情况，反正我是等了很久.......

* 5.测试是否安装完毕

```
输入  hexo generate(或 hexo g)
```

初始化本地文件，这时候hexo会把我们的源文件转化为静态网页，

然后

```
输入  hexo server(或 hexo s)
```

{% asset_img 20161123213034.png [] %}
意思是在本地创建一个服务器用了展示我们生成的网页，这一步是非常方便的，以后我们有任何修改只需在本地查看，确保正确后再发布到github上。

之后打开你电脑上的浏览器，输入http:\\localhost:4000访问，能显示就说明大功告成啦，安装完毕！！！

### 将本地项目源文件上传到Github上