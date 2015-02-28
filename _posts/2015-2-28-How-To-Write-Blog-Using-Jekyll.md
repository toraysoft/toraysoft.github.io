---
layout: post
title: 如何使用Jekyll发布文章到图锐实验室博客？
---

* 准备工作
  1. 使用自己的github账号fork <https://github.com/toraysoft/toraysoft.github.io>
  2. 把第一步的仓库克隆到本地
* 新建一篇博客
  1. 打开本地仓库中的_posts目录
  2. 在_posts目录中新建一个Markdown文件

        ```
        命名格式为: YEAR-MONTH-DAY-title.md
        例如: 2014-02-28-hello-world.md
        ```
  3. 编辑刚刚创建的文件，在头部加入以下代码：

        ```
        ---
        layout: post
        title: Hello World!
        ---
        ```
        其中layout代表页面的布局，一般不需要改变，写post就可以。title代表文章的标题。
  4. 在第二个---符号以下地方添加正文内容，内容支持Markdown格式。Markdown语法请参考这篇文章：<https://guides.github.com/features/mastering-markdown/>

* 本地预览
  1. 安装Jekyll以及相关插件

        ```
        安装命令：gem install github-pages
        ```
  2. 进入本地仓库所在目录，执行

        ```
        jekyll serve
        ```
  3. 使用浏览器打开 <http://127.0.0.1:4000>
  
* 发布流程  
  1. 新建或修改博客，并提交到第一步创建的仓库中
  2. 发起Pull Request，等待审核人员合并代码即可
