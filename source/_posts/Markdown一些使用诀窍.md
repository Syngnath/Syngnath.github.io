---
title: Markdown一些使用诀窍
categories:
  - 随笔
comments: true
img: 'https://synp.oss-cn-hongkong.aliyuncs.com/markdown.png'
date: 2019-05-25 12:19:00
---

# Markdown阅读器
这里推荐 `VsCode` ，微软老牌阅读器 
vscode要安装增强插件来更好支持markdown

## 插件：
GitLens -- Git分支显示
Markdown Preview Enhanced -- 支持Markdown外部导入
vscode-pdf --支持pdf显示
MarkdownAllinOne -- 快捷键

## 相关文章
[Markdown基本语法](https://www.jianshu.com/p/191d1e21f7ed)
[Markdown增强插件使用说明](https://shd101wyy.github.io/markdown-preview-enhanced/#/)

# Markdown语法
```
# 一级标题
## 二级
### 三级

分割线：
---
- 列表1级
    - 列表二级Tab + “-”
        - 列表三级
         

- 2.afwaf
-有一个以上就行
-两边加：表示文字居中
-右边加：表示文字居右

姓名|班别|学号
:--:|:--:|:--:
yeshujun|2-class|1502
moss|3-class|1504


姓名|班别|学号
--|--|--
yeshujun|2-class|1502
moss|3-class|1504

引用,可以嵌套:
> 来自XXX
>> 来自YYY
>>> 来自ZZZ

加粗：
**你好**

斜体:
*你好*

斜体加粗:
***你好***

删除线：
~~你好~~

换行：
<br/>

插入图片
![名称可不填](路径)

# MarkdownAllinOne快捷键
Ctrl + B	Toggle bold  **测试**
Ctrl + I	Toggle italic *测试*
Ctrl + Shift + ]	Toggle heading (uplevel) 
Ctrl + Shift + [	Toggle heading (downlevel)

Ctrl + M	Toggle math environment
$f=1+x$

Alt + C	Check/Uncheck task list item

Ctrl + Shift + V	Toggle preview /off
```