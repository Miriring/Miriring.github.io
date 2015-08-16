---
layout: post
title:  "yoman和gulp"
date:   2015-08-1 16:20:05
categories:  automation
excerpt: 自动化 架构 yoman gulp 
---

* content
{:toc}

##简介  
  这次主要把最近使用yoman和gulp的经验反思一下，顺便介绍下安装过程，希望看过后能避免我走过的坑。

---

##yoman
yoman作为一个由yo,grunt,bower的集成方案，完成了项目的整个初始化和基础框架。其中yo通过使用各种generate完成了项目的文件结构和各种生产环节需要的库与框架的引入，基本完成了项目需要的基本结构（应该就是所谓脚手架的意思吧），grunt作为自动化流程，把各种任务自动化完成。bower作为前端管理包，框架的工具，与npm比就更专注点吧。

##yoman使用

* 我现阶段使用的感觉是yo创建的基本框架确实包含了一个项目能用到的东西，结构更加清晰，引入的工具齐全。但对我来说整个文件和结构还是过于庞杂了点，很多对于现阶段我来说还是用不到的，所以我觉得如果不是一个人做完整的大型项目，yo的引用比起自己建立的小巧结构和必要的文件要不实用的多。

* grunt是亮点，压缩，前缀，重命名，检查这些功能确实经过自己配置后使用起来顺心很多。

* bower与npm差不多，不过bower从github上下载，可能资源方面更有优势

** npm安装 **

*主要是通过npm就能引入了所以主要讲下npm的坑

  1. 先下载了node.js就会自带npm了。

  2. 由于被墙了,npm不翻墙的话基本只能用淘宝的镜像, 只需要在npm install XXX 后加入--registry=https://registry.npm.taobao.org 就会引用淘宝的地址进行下载。

  3.建议导入包都采用加--registry=https://registry.npm.taobao.org 后缀的形式，因为我一开始用了$ npm install -g cnpm --registry=https://registry.npm.taobao.org 引入了淘宝的cnpm，但是用cnpm它是将下载的包放入node.js的node_modules里，但是npm是放入users里APPDATA这个隐藏文件夹的，在环境变量中引用的也是这个隐藏文件夹。这个就导致可能你下载的generate找不到，反正我是没找到。建议还是用npm导入，同时检查环境变量里NODE_PATH的path和PATHL里对于modules的地址是否相同，我因为超自然原因两个引用不同，NODE_PATH是我很久前下载后删掉的nodejs的地址，这样弄死也找不到下载的包，所以用淘宝镜像时以上两点要多多注意


*更有爱的gulp

我目前采用的是gulp而非grunt，用之前就算你听说gulp是流的方式，配置更简单，使用更流畅。然而你并不会有太多感受，徘徊于grunt与gulp之间，愁肠千
