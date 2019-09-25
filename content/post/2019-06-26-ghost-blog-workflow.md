---
title: Ghost Blog Workflow
author: wyih
date: '2019-06-26'
slug: ghost-blog-workflow
categories:
  - Workflow
tags:
  - Workflow
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T16:40:38+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
 Sep 25, 2019 的update: 这个WorkFlow不太完美，现在转用Blogdown和Git来管理，正在摸索中。
 
~~总算把Ghost配得七七八八，以后要好好记下笔记了。像以前看过的东西时间久了就全忘了，太郁闷了。~~

## 目前的Workflow如下

- 在Synology Drive下Draft目录存放草稿
- Typora里写markdown并保存
- 存Leanote和evernote各一份，这个应该可以通过[IFTTT](https://ifttt.com/)来实现，日后研究
- 另外一个解决方案是直接Git init Draft目录，再往[Github](github.com)上push备份。
- 存Ghost发布

## 需要的代码注入

### 公式

在`Post Header` 粘贴以下脚本

```js
<script type="text/javascript" src="https://cdn.bootcss.com/mathjax/2.7.3/latest.js?config=TeX-AMS-MML_HTMLorMML"></script>  
<script type="text/x-mathjax-config">  
    MathJax.Hub.Config({
        tex2jax: {
            inlineMath: [['$$','$$'], ['\\\\(','\\\\)']],
            processEscapes: true
        }
    });
</script>  
```

### 语法高亮
在`Post Header` 粘贴以下脚本
```js
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/prism/1.16.0/themes/prism-tomorrow.css">
```
在`Post Footer`粘贴以下脚本
```js
<script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.16.0/prism.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.16.0/components/prism-python.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.16.0/components/prism-r.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.16.0/components/prism-sas.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.16.0/components/prism-bash.js"></script>
```
[prism.js](https://prismjs.com/)不支持Stata就凑合着用用吧。需要载入的[components](https://cdnjs.com/libraries/prism)取决于博文需要。

## 需要注意的地方

- Ghost对于H1不能生成Toc，从H2开始
- 对于Markdown中的公式有些需要[转义](https://blog.yhong.wang/gong-shi/)