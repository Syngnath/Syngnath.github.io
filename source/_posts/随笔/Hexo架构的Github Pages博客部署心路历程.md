---
title: Hexo架构的Github Pages博客部署心路历程
categories:
  - 随笔
tags:
  - Travis Cl
  - Hexo
  - VsCode
comments: true
img: 'https://synp.oss-cn-hongkong.aliyuncs.com/welcome.jpg'
abbrlink: 34807
date: 2019-05-22 18:26:00
---

# 简介
欢迎来到我的github博客，这个博客会被我用于记录一些学习笔记，将来可能会发布一些总结性的东西。



# 博客简历心路历程
这个博客弄了我两天，终于弄完了，这是第一个笔记，主要是不太熟悉一些命令操作踩坑了，并且实现自动化部署花了些时间。

找了好多主题不太满意，最后决定用这个主题是因为说明文档比较详细，但是直接copy进去是用不了的...所以又看了好多文章才实现。

现在我在本地有两个工作区，第一个是自己随笔写的自己看的，比较多东西，第二个是发布出去到博客上的上的，是需要花时间整理发布的。为了之后好说明，将前者设为**A工作区**，后者设为**B工作区**



## A工作区方案:Onedrive + VSCode + gitlab
Onedrive大家应该有用过，就是一个非常方便的同步工具，设置好文件夹好东西扔进去就能自动同步，而且不会怕锁定文件一切自动化不用理

VsCode主要是用于码字和git推送，需要装一些插件才能更好支持git和markdown，这里不再阐述

gitlab用于git托管，并且支持私有仓库，让你的隐私不被别人看到，国内的码云也可以

好了，现在资料本地一份，Onedrive云盘一份，gitlab一份，三重备份，还能随时回溯，nothing is better!



## B工作区方案: VScode + hexo + travis cl + Snippet
简单说下，创建一个git工作区后，安装hexo，它会生成静态网页，这样我们可以本地预览或者推送到GitHUb或者码云等Git托管网站，它们有个Github pages的功能可以显示出该静态网页，从而实现Blog的功能。但是有人可能会问**travis cl**是个什么东西？

你想一下，每次本地码完字后，都要去工作区
```
hexo clean && hexo g
hexo d
```
是不是感觉太麻烦，我只想安安静静写个文章而已。
所以travis cl可以帮你解决这个问题，
它会监控你的分支，当你push东西上去后，会自动帮你deploy一个静态网页，这样你就不用每次都输入命令了。

好了你现在要做的就是在vscode里面写好文章，确定没问题后就在vscode里push上去，ok！搞定

接下来介绍一下**Snippet**这个主题，这个主题是我见过最好的hexo主题框架，有钉钉通知推送，评论区功能也集成了N个插件，基本上你想要的它都有了，具体的不再评论，对应的使用手册很详细。



## 踩坑
坑1：hexo部署后的文件覆盖掉原来在master上的源文件
解决：把源文件整个文件夹Push到master后，创建分支hexo,然后把hexo设为默认分支，然后本地部署时，它自动推送的依然是master分支,这这样就不会影响到hexo的源文件

坑2：npm install 没反应，啥提示都没，导致下一步无法进行下去
解决：npm版本太低，去下载新版本的nodejs，一些博客推荐的版本都是上古版本

坑3: travis cl 部署失败，查看日志发现 rakefiles not found
解决：不要用主题包自带的`.travis.yml`文件，它会跳转到`_travis.sh`里面执行一大堆命令，注意要一句一句看把不需要的去掉，如果出错仔细阅读日记，尝试把出错导致语句去掉。

**注意GH_TOKEN，和GH_REF等${}里面装着一个字符串是你要在travis.cl网站里面对应工程setting里添加环境变量，它读取时就会自动替换，这样好处是防止泄露重要资料**
**但是有时候在travis加了环境变量也会失效还是要把字符串粘贴到文本中，这个具体对应功能不行再尝试**

好了，希望你接下来和我一样搭建出好的Blog，下面提供一些参考博客
[最全Hexo博客搭建+主题优化+插件配置+常用操作+错误分析](https://www.simon96.online/2018/10/12/hexo-tutorial/)
[Travis Cl参考](https://zespia.tw/blog/2015/01/21/continuous-deployment-to-github-with-travis/)
[valine评论部署](https://deserts.io/diy-a-comment-system/)



# 结论
现在工具流是在VsCode里码好字，使用VsCode管理两个Git工作区，然后挑选几篇笔记整理成文章，复制到对应的文件夹，推送。Done！看着窗外的月光1分钟后，Travis CL会自动帮我构建环境生成静态网页，并且通过钉钉(邮件)通知我构建成功。GitHub博客就可以访问到最新的文章区，如果有问题，也可以在下面的评论区进行评论。多么美好的工作流。好了，今天的博客介绍就到这里!



# 博客内容
会记录一些关于C家族笔记和Unity相关东西，AI算法等，主要和游戏相关。
`Study for developing our country`