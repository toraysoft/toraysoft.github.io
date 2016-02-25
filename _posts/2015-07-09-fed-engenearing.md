---
layout: post
title: "浅谈前端工程化"
---

## 什么是前端工程化
我理解的前端工程化是根据前端开发的经验常识，总结出一套完整的流程规范，并建立可重复创造优秀产品的开发环境。

## 为什么要工程化前端，工程化的原因
让代码符合可重用、可扩展、DRY的原则。

## 前端工程化需要包括什么，怎么实现
前端工程包括`开发规范`、`模块化开发`、`组件仓库`、`性能优化`、`开发工具`、`项目部署`、`测试`等部分。以下通过`模块化开发`和`性能优化`来讲讲如何实现前端工程。

### 1、模块化开发

### 1.1、Javascript模块化——CommonJS规范
CommonJS制定了一系列规范来定义模块化的js。Nodejs采用CommonJS规范来管理服务器模块。

根据CommonJS规范，一个单独的文件就是一个模块，每一个模块中的作用域是独立的，模块中的变量、函数是模块私有的。规范规定了每个文件对外的接口是`module.exports`，该对象的属性和方法均可被其他模块使用`require`方法导入。

CommonJS定义的模块如下：
        
        // myModule.js
        var path = require('path');
        var myModule = {};

        myModule.joinPath = function () {
            return path.join.apply(path, arguments);
        };

        module.exports = myModule;

        // 其他文件中引入myModule
        var module = require('myModule');

CommonJS规范中`require`函数返回依赖的对象是同步的，这在服务端是没有问题的，因为文件存储在服务器硬盘中，读取速度快。但是在浏览器中要使用CommonJS规范就不行了，文件的读取速度取决于网速，文件有可能要读取很长时间，造成浏览器假死。由此便催生出AMD（Asynchronous Module Definition，异步模块定义）和CMD（Common Module Definition，通用模块定义）。

目前的前端框架中，Requirejs是AMD规范的代表，Seajs是CMD规范的代表。他们两者都是根据CommonJS的模块化开发思想而来，用于管理前端组件依赖关系。使用`require`或`define`函数引入和定义模块。

Requirejs定义模块的代码如下：

        // myModule.js
        define(function () {
            return {
                sayHello: function () {
                    console.log('myModule: hello');
                }
            };
        });

        // main.js
        require(['myModule'], function (myModule) {
            myModule.sayHello(); // 'myModule: hello'
        });

Seajs定义模块的方式和Requirejs基本一致，只是引入依赖的写法有些区别：

        // main.js
        define(function (require, exports, module) {
            var myModule = require('myModule');
            myModule.sayHello(); // 'myModule: hello'
        });

Requirejs和Seajs主要的区别在于前者是预先加载策略，也就是异步加载依赖，后者是懒加载策略，即同步加载依赖。在模块工厂函数的写法上Seajs更贴近Nodejs。

### 1.2、OOCSS——面向对象css实现

OOCSS（Object Oriented CSS）是一种css的编码规范，它提倡将视觉上重复的模块，抽象成独立片段的HTML、CSS、Javascript，作为一种能在绝大多数网站中都适用的对象。（[参考资料](http://code.tutsplus.com/tutorials/object-oriented-css-what-how-and-why--net-6986)）

其中主要有两个原则：

    * 结构和样式分离
    * 容器和内容分离

**结构和样式分离** 是指将样式特性抽离出来，作为一种修饰样式。比如将color、background这些修饰属性和width、height这些结构属性分开，可以使用两个样式进行管理。比如

        .btn-submit {
            padding: 8px 12px;
            width: 100px;
            height: 100px;
            color: #fff;
            text-align: center;
            background: #f00;
        }
        .btn-ok {
            padding: 8px 12px;
            width: 100px;
            height: 100px;
            color: #eee;
            text-align: center;
            background: #0f0;
        }

这两个按钮的样式代码可以抽象成`.btn`结构样式和`.btn-red`、`.btn-green`修饰样式来管理，提高css重用性。修改后的css如下：

        .btn {
            padding: 8px 12px;
            width: 100px;
            height: 100px;
            text-align: center;
        }
        .btn-red {
            color: #fff;
            background: #f00;
        }
        .btn-green {
            color: #eee;
            background: #0f0;
        }

**容器和内容分离** 是指确保任何容器对象都要能接受任何对象作为其内容。比如有一个包裹着标题`<h1>`的容器`.container`结构和样式如下：

        <!-- html -->
        <div class="container">
            <h1 class="h1">Hello world</h1>
        </div>

        /* 容器和内容分离的css */
        .container {
            margin: 0 auto;
        }
        .h1 {
            margin: 15px 0;
            font-size: 36px;
            font-weight: 700;
        }
        /* 容器和内容不分离的css */
        .container {
            margin: 0 auto;
        }
        .container h1 {
            margin: 15px 0;
            font-size: 36px;
            font-weight: 700;
        }

上面第一种css将容器和元素的样式分离成两个class，这样更利于css的复用；而第二种css将html元素名引入了css，如果html结构稍微改变可能就不适用了。

### 1.3 Less——动态样式语言语言实现OOCSS
Less为CSS赋予了动态语言的特性，如变量、继承、运算、函数。使用动态样式语言可以更好地实现OOCSS。（[中文参考资料](http://www.bootcss.com/p/lesscss/)、[官网](http://lesscss.org/)）

**变量** 可以单独定义通用的样式，在代码中重复使用：

    // LESS

    @yellowgreen: #A6E22E;

    .header {
      color: @yellowgreen;
    }
    .title {
      color: @yellowgreen;
    }

    /* 生成的 CSS */

    #header {
      color: #A6E22E;
    }
    h2 {
      color: #A6E22E;
    }

**嵌套规则** 能在选择器中嵌套另一个选择器实现样式定义，这种方式在定义嵌套层级较多的选择器时非常方便，结构更清晰，代码更简洁。

        // LESS
        @nav-link-padding: 15px;
        @nav-link-hover-bg: #fff;
        .nav {
          margin-bottom: 0;
          padding-left: 0;
          list-style: none;

          > li {
            position: relative;
            display: block;

            > a {
              position: relative;
              display: block;
              padding: @nav-link-padding;
              &:hover,
              &:focus {
                text-decoration: none;
                background-color: @nav-link-hover-bg;
              }
            }
          }
        }

        /* 生成的 CSS */
        .nav {
          margin-bottom: 0;
          padding-left: 0;
          list-style: none;
        }
        .nav > li {
          position: relative;
          display: block;
        }
        .nav > li > a {
          position: relative;
          display: block;
          padding: 15px;
        }
        .nav > li a:hover,
        .nav > li a:focus {
          text-decoration: none;
          background-color: #fff;
        }

**混合（mixin）** 可以将定义好的样式结构添加进其他的选择器中，而且还可以通过传入参数控制样式内容，就像使用函数一样。

        // mixin
        .button-size(@padding-vertical; @padding-horizontal; @font-size; @line-height; @border-radius) {
          padding: @padding-vertical @padding-horizontal;
          font-size: @font-size;
          line-height: @line-height;
          border-radius: @border-radius;
        }

        .btn-submit {
            .button-size(8px, 12px, 14px, 1.5, 5px);
        }

**函数和运算** Less提供了基本的加、减、乘、除运算，可以用作属性值和颜色的计算。同时Less提供一些Color函数进行颜色值计算，Math函数处理数值运算。

        // 返回@color亮度增加了10%的颜色值
        lighten(@color, 10%);

        // 返回@color亮度减少了10%的颜色值
        darken(@color, 10%);

        // 返回@color1和@color2颜色的混合值
        mix(@color1, @color2);

        .panel {
            padding: @padding-horizontal (@padding-horizontal * 1.2);
        }

**命名空间** 能更好封装mixin和变量。

        #gradient {
          .vertical(@start-color: #555; @end-color: #333; @start-percent: 0%; @end-percent: 100%) {
            background-image: -webkit-linear-gradient(top, @start-color @start-percent, @end-color @end-percent);  // Safari 5.1-6, Chrome 10+
            background-image: -o-linear-gradient(top, @start-color @start-percent, @end-color @end-percent);  // Opera 12
            background-image: linear-gradient(to bottom, @start-color @start-percent, @end-color @end-percent); // Standard, IE10, Firefox 16+, Opera 12.10+, Safari 7+, Chrome 26+
            background-repeat: repeat-x;
            filter: e(%("progid:DXImageTransform.Microsoft.gradient(startColorstr='%d', endColorstr='%d', GradientType=0)",argb(@start-color),argb(@end-color))); // IE9 and down
          }
        }

        // 使用
        .btn-submit {
            #gradient .vertical(#555, #333, 0%, 100%);
        }

**继承** 可以将一个选择器的样式混合进被继承的选择器，继承通过向伪类`:extend`传入选择器来实现。

        .btn-red {
            color: #f00;
            font-size: 12px;
        }
        // 继承.btn-red
        .btn-submit:extend(.btn-red) {
            font-size: 16px;
        }

        /* output */
        .btn-red,
        .btn-submit {
            color: #f00;
            font-size: 12px;
        }
        .btn-submit {
            font-size: 16px;
        }

**Importing** 支持通过进入其他的LESS/CSS文件，实现样式模块化。

        @import "lib.less";
        @import "lib.css";
        @import "lib";

### 2、项目发布与优化

### 2.1、Grunt介绍
Grunt是基于Node.js的项目构建工具，对于需要反复重复的任务，例如压缩、编译、单元测试、linting等，它可以自动化地完成，简化前端开发的工作。Grunt拥有众多插件，可以满足多种代码发布需求。（[官网](http://www.gruntjs.net/)，[Grunt插件](https://github.com/gruntjs/grunt-contrib)）

一个Grunt项目根目录下要包含`package.json`和`Gruntfile.js`。
    
* **package.json** 是Nodejs项目的包管理文件，里面包含了项目名称、版本号、作者信息、依赖模块等信息。该文件中的依赖模块配置项`devDependencies`可以列出依赖的Grunt插件。
* **Gruntfile** 是Grunt用来配置或定义任务（task）并加载Grunt插件的。

`package.json`的配置代码如下，配置项`devDependencies`中列出了Grunt和依赖的Grunt插件。

        {
          "name": "my-project-name",
          "version": "0.1.0",
          "devDependencies": {
            "grunt": "~0.4.5",
            "grunt-contrib-clean": "~0.5.0",
            "grunt-contrib-uglify": "~0.5.0",
            "load-grunt-tasks": "~0.6.0"
          }
        }

在项目一开始的时候这些依赖可能是没有的，这时候需要先将`package.json`中依赖的模块下载下来：

        npm install

在`Gruntfile`中加载Grunt插件，配置插件和任务设置。代码示例如下：

        module.exports = function (grunt) {
            // 项目配置
            grunt.initConfig({
                pkg: grunt.file.readJSON('package.json'),
                clean: {
                    dist: ['dist/js/*']
                },
                uglify: {
                  options: {
                    preserveComments: 'some'
                  },
                  bootstrap: {
                    src: 'dist/js/<%= pkg.name %>.js',
                    dest: 'dist/js/<%= pkg.name %>.min.js'
                  }
                }
            });

            // 获取load-grunt-tasks模块，将package.json中的Grunt插件依赖自动加载进来
            require('load-grunt-tasks')(grunt, { scope: 'devDependencies' });

            // 自定义任务
            grunt.registerTask('dist-js', ['clean', 'uglify:bootstrap']);
        };

在命令行中输入命令，调用Grunt任务：

        # 调用自定义任务
        grunt dist-js

        # 直接调用Grunt插件的任务
        grunt uglify:bootstrap

### 2.2、grunt-contrib-uglify：实现js文件的组合和压缩

### 2.3、grunt-contrib-less：将less代码编译成css代码

### 2.4、grunt-autoprefixer：为样式根据浏览器的兼容要求，自动增加浏览器前缀

### 2.5、grunt-contrib-cssmin：压缩css代码

### 2.6、grunt-jsdoc：根据注释生成js文档

