---
layout: post
title:  "深入了解ECNAScript上"
date:   2015-01-1 12:06:05
categories:  JavaScript
excerpt: Javascript基本语法 引用传递 作用域 
---

* content
{:toc}

##简介
  主要集中在深入了解上，所以就只列举了ECNAScript里最为重要，难以理解的部分，对于基础知识较少涉及。
  
---

##标识符

  * 首先ECNAScript一切都是区分大小写的，并且构造函数的第一个首字母一般大写
  
  * 标识符指的是变量，函数，属性的名字，并且有部分保留字不能使用。 
  >
  > **关于这点想强调的是:** 初学者（像我）会分不清什么时候用字符串（带引号），什么时候不带引号（标识符），倒至写参数和引用变量时总傻傻分布清楚。我觉得一个原则，如果不带引号，就会被当作标识符解析，会在作用域链上引用之前设置的变量，函数，而属性也是会往原型链上查找的。如果没有引用的话，只是传一个确定值的情况下就用字符串（不带引号）。
  
##数据类型

  * 五种基本类型:Undefined,Null,Boolean,Number,String，一个复杂类型：Object。
  对于五种基本类型而言是值类型，在变量传递的时候使用的是值传递，可以看作是复制了一分一模一样的副本，该副本与原来的值 完全独立
  **示例:**
      #var a=10;
      #b=a;
      #b=5;
      #alert(a) //a==5
      #从上可以看出，a是把一个值得副本给了b，a与b拥有的是彼此独立的10这个值