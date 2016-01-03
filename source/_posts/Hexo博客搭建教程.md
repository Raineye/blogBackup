title: Hexo博客搭建教程
date: 2015-12-30 16:38:02
tags:
	- hexo
---

### [hexo](https://github.com/hexojs/hexo)是一款基于Node.js的静态博客框架。目前在GitHub上已有7857 star 和 1308 fork。

<!-- more -->

<br/>

### 环境
1. 安装git
Windows去下载[gitBash](http://git-scm.com/download/),至于怎么安装自己找教程吧。

2. 安装node.js
去[node官网下载](https://nodejs.org/en/download/)下载node安装包安装

3. 用npm获取hexo的套件


	$ npm -g install hexo

### 创建hexo博客
1. 初始化博客


	$ hexo init <folder>

`<folder>`即你要创建博客的文件夹，你同样可以省略它在当前目录建立博客

2. 生成静态网站


	$ hexo generate

3. 开启服务


	$ hexo server

服务会跑在本机localhost:4000

### 添加文章

1. 使用命令


	$ hexo new <title>	

`<title>`是你文章的标题，然后hexo会帮你生成一个文件`<title>.md`在`<folder>/source/_posts/`下面,用markdown语言写就是了。markdown语言的语法可以去网上搜一下资料，十分简单。

2.这里说明一下文章中简单的数据格式：

	title: Hexo博客搭建教程 		//你的标题
	date: 2015-12-30 16:38:02 	//文章创建的时间戳
	tags:						//文章的标签可以添加多个
		- hexo
		- git

### 使用gitcafe托管静态博客

首先,你需要注册一个 [gitcafe](https://gitcafe.com) 账号,<username>可以起一个自己喜欢的。

1.新建一个仓库
填写好仓库信息，最好取一个有意义的名字`<repositoryName>`**未来你的博客域名会是`<repositoryName>.gitcafe.io`**

![图片走丢了](http://7xpp66.com1.z0.glb.clouddn.com/blog/img/20160101.png)

2.在项目中增加hexo-deployer-git

	$ npm install hexo-deployer-git --save

然后修改配置文件`<frlder>/_config.yml`

	deploy:
	  	type: git
	  	repository: git@gitcafe.com:<username>/<repositoryName>.git
	  	branch: gitcafe-pages
	  	message: Site updated

3.发布你的网站

	$ hexo deploy

3.好了，现在可以访问`<repositoryName>.gitcafe.io`来查看你的网站了 









