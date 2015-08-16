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

**npm安装**

* 主要是通过npm就能引入了所以主要讲下npm的坑

  1. 先下载了node.js就会自带npm了。

  2. 由于被墙了,npm不翻墙的话基本只能用淘宝的镜像, 只需要在npm install XXX 后加入--registry=https://registry.npm.taobao.org 就会引用淘宝的地址进行下载。

  3.建议导入包都采用加--registry=https://registry.npm.taobao.org 后缀的形式，因为我一开始用了$ npm install -g cnpm --registry=https://registry.npm.taobao.org 引入了淘宝的cnpm，但是用cnpm它是将下载的包放入node.js的node_modules里，但是npm是放入users里APPDATA这个隐藏文件夹的，在环境变量中引用的也是这个隐藏文件夹。这个就导致可能你下载的generate找不到，反正我是没找到。建议还是用npm导入，同时检查环境变量里NODE_PATH的path和PATHL里对于modules的地址是否相同，我因为超自然原因两个引用不同，NODE_PATH是我很久前下载后删掉的nodejs的地址，这样弄死也找不到下载的包，所以用淘宝镜像时以上两点要多多注意


* 更有爱的gulp

  我目前采用的是gulp而非grunt，用之前就算你听说gulp是流的方式，配置更简单，使用更流畅。然而你并不会有太多感受，徘徊于grunt与gulp之间，愁肠千结。我个人认为对于小白gulp更加实用，因为你可以更方便的进行gulpfile的配置，上手速度极快，可以更好的配置适合你的工作流。
  
  这里就把我自己先阶段的配置文件发上，我不加watch的部分，这样大家可以更好的配置自己的gulpfile
  
  **首先是package.json**
  <pre><code class="markdown"> 
   {
    "name": "gulp-now",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "author": "csp",
    "license": "ISC",
    "devDependencies": {
      "gulp": "^3.9.0",
      "gulp-autoprefixer": "^2.3.1",
      "gulp-clean": "^0.3.1",
      "gulp-concat": "^2.6.0",
      "gulp-htmlmin": "^1.1.3",
      "gulp-imagemin": "^2.3.0",
      "gulp-jshint": "^1.11.2",
      "gulp-minify-css": "^1.2.0",
      "gulp-rename": "^1.2.2",
      "gulp-uglify": "^1.2.0"
       }
    }
  </code></pre>
  
  **这是我的gulpfile.js**
        <pre><code class="markdown">
          var gulp = require('gulp'),
          imagemin = require('gulp-imagemin'),
          cssmin = require('gulp-minify-css'),
          htmlmin = require('gulp-htmlmin'),
          concat = require('gulp-concat'),
          uglify = require('gulp-uglify'),
          jshint = require('gulp-jshint'),
          clean = require('gulp-clean'),
          autoprefixer = require('gulp-autoprefixer'),
          rename = require('gulp-rename');
      //图片压缩
      gulp.task('imagemin', function () {
          gulp.src('src/img/*.{png,jpg,gif,ico}')
              .pipe(imagemin({
                  optimizationLevel: 5,
                  progressive: true, 
                  interlaced: true, 
                  multipass: true 
              }))
              .pipe(gulp.dest('dist/img'));
      });
      //css增加浏览器前缀后压缩
      gulp.task('cssmin', function () {
          gulp.src('src/css/*.css')
              .pipe(autoprefixer({
                  browsers: ['last 2 versions']
              }))
              .pipe(cssmin({
                  advanced: false,
                  compatibility: 'ie7',
                  keepBreaks: false
              }))
              .pipe(rename({ suffix: '.min'}))
              .pipe(gulp.dest('dist/css'));
      });
       //js和并后压缩
      gulp.task('jsmin', function (){
           return gulp.src('src/js/*.js')
              .pipe(concat('all.js'))
              .pipe(gulp.dest('dist/all-js'))
              .pipe(uglify())
              .pipe(rename('all.min.js'))
              .pipe(gulp.dest('dist/js'));
      });
      //js检查
      gulp.task('jshint', function () {
          return gulp.src('src/js/*.js')
              .pipe(jshint())
              .pipe(jshint.reporter('default'));
      });
      //html压缩
      gulp.task('htmlmin', function () {
          var options = {
              removeComments: true,
              collapseWhitespace: true,
              collapseBooleanAttributes: false,
              removeEmptyAttributes: true,
              removeScriptTypeAttributes: true,
              removeStyleLinkTypeAttributes: true,
              minifyJS: true,
              minifyCSS: true
          };
          gulp.src('src/html/*.html')
              .pipe(htmlmin(options))
              .pipe(gulp.dest('dist/html'));
      });
      
      //清空文件夹
      gulp.task('clean', function() { 
        return gulp.src(['dist/css', 'dist/js', 'dist/img','dist/all-js','dist/html'], {read: false})
          .pipe(clean());
      });
      
      //默认执行的任务，并且先执行clean任务
      gulp.task('default', ['clean'], function() { 
          gulp.start('cssmin','jsmin','htmlmin','imagemin');
      });
  </doce></pre>
