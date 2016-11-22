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

### 本地搭建Hexo博客





