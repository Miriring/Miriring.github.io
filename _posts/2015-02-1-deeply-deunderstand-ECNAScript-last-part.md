---
layout: post
title:  "深入了解ECNAScript下"
date:   2015-08-3 12:17:06
categories:  JavaScript
excerpt: Javascript作用域链  原型  组合式构造函数  寄生组合式继承  闭包
---

* content
{:toc}

## 作用域链
  
  * 作用域链的产生和标识符搜索
  
  我将执行环境统一说成作用域，首先JS只有函数作用域和全局作用域，而在当前作用域会有一个与之关联的变量对象，这个对象包含
  了在本作用域下定义的所有变量，包括参数和函数，全局作用域是最外层的作用域。作用域运行时有一个按顺序链接所有变量对象的
  作用域链参数，作用域链的前端是当前作用域下的变量对象，越外层的作用域的变量对象越靠后直到最外层的全局变量对象。当当前
  函数需要访问变量时，沿着作用域链由前往后搜索标识符，直到找到位置，如果在全局作用域时都没找到返回Undefined，但是搜索
  过程不能逆向，也就是说外层作用域无法访问里层作用域的变量。并且标识符搜索同原型一样，遵从就近原则，如果前面搜索到了，
  就不再往后搜索了。
  
  **注意** 当本作用域函数执行完后本作用域函的变量对象就被销毁，全局作用域的变量是浏览器或页面关闭时销毁
  
  * 延长作用域链
  
  两个语句作用域将延长
    1. try-catch语句的catch块
    2. with语句
    对于catch来说是创建了一个包含抛出错误的新对象至作用域链前端，对with来说在作用域链前端添加指定的新对象
    
    
##原型与原型链
  
我尽量用简洁的语言解释原型，上图反而晕
  
* 理解原型链的形成
    
1. 每一个函数都有一个属性prototype，该属性指向该函数的原型对象。而每个原型对象都有一个constructor属性，指向
函数本身。 这个函数与对应的原型对象就互相引用了
    
2. 通过函数可以创建一个实例对象，创建的对象都有一个属性[Prototype]（是个指向原型的指针），指向构造函数的原型对象，
    
3. 通过上面，实例对象的[Prototype]属性就指向了构造函数的原型，所以当用一个构造函数创建实例对象并将其赋给一个
函数的原型对象的时候，该函数的原型对象就通过[Prototype]指向了构造函数的原型对象，最后用该函数创建实例对象后，
所创建的实例对象当然指向该函数的原型对象。 
**总结：** 该函数实例对象指向该函数的原型对象，该函数的原型对象又指向父构造函数的原型对象，由此形成原型链。  
**注意：** 其实只需把握一个原则，构造函数创建的实例对象通过[Prototype]都将指向构造函数的原型对象
    
**示例1**
    
        var Person = function () { };
        Person.prototype.Say = function () {
            alert("Person say");
        }
        Person.prototype.Salary = 50000;
        var Programmer = function () { };
        Programmer.prototype = new Person();
        Programmer.prototype.WriteCode = function () {
            alert("programmer writes code");
        };
        Programmer.prototype.Salary = 500;
        var p = new Programmer();
        p.Say();          //Person say
        p.WriteCode();    //programmer writes code
        alert(p.Salary);  //500
        
**注意：** 原型链的搜索与作用域链的搜索一样，当前面找到了就往后搜索了，所以p.Salary是500
**注意：**  把新对象赋给原型对象的同时将导致constructor的修改，需要手动把constructor改回
    
* 原型的共享
    
通过原型链我们可以想作用域链一个向外逐级搜索属性与方法，由于是引用的性质，所以同样的方法都是来自一个对象上的
方法与属性是共享的而不是副本，类似与变量引用传递的关系。可以参考上面的示例1

**贴士** 无论创建对象还是继承，都遵循将私有的数据与方法放入构造函数中，做到互不影响，将公用的方法与数据放入原型对象
中做到共享和复用。 构造函数中的可以看作是值传递，原型对象上的是引用传递。

##组合式构造函数

直接说最常用的好了，然后再分析为什么

**示例2**
        function Person(name,age,job){
          this.name=name;
          this.age=age;
          this.friend=["Miriring","csp"];
        }
        
        Person.prototype={
          constructor:Person,
          sayName:funtion(){
            alert(this.name);
          }
        }
        
        var person1=new Person("me",23."student");
        var person2=new Person("he",25,"PM");
        person1.sayName();  //me
        person1.friend.push("Van");
        alert(person1.friend);  //Miriring,csp,Van
        alert(person2.friend);  //Miriring,csp
        alert(person1.sayName===person2.sayName); //true
        
由上可以看出通过将对象专有的属性和方法定义在构造函数中，创建对象时将
以副本的形式复制到对象上，每一个对象都拥有自己的副本，彼此独立互不影响，所以person1和person2的friend数组并不相同。
而将需要共享的方法定义在原型上，通过引用的方式去调用同一个方法，提高了
方法的复用性，并且有该构造函数创建的对象都可以通过构造函数的原型对象访问到该方法。所以person1.sayName===person2.sayName为true。


## 寄生组合式继承 

先上示例

**示例3**
       
       function SurperType(name){
        this.name=name;
        this.color=["red","blue","green"];
        };
        
        SuerType.prototype.sayName=funtion(){
          alert(this.name);
        };
        
        funtion SubType(name,age){
          SurperType.call(this,name);
          this.age=age;
        }
        
        inheritPrototype(SubType,SuperType);
        
        SubType.prototype.sayAge=funtion(){
          alert(this.age);
        };
        
        funtion  inheritPrototype(subType,superType){
          var prototype=object(surperType.prototype);
          prototype.constructor=subTyoe;
          subType.prototype=prototype;
        }

1. 构造函数通过SurperType.call(this,name); 用call（）将this指向本构造函数，调用了父构造函数的代码，完成私有属性与方法
的拷贝

2. 通过inheritPrototype(）函数，首先创建一个包含surperType原型对象的新对象，然后修改constructor指向，最后将新对象赋给
subType的原型对象完成引用。  
  等于通过直接将父构造函数的原型对象直接赋给字构造函数的原型对象完成了共有方法和属性的继承，由于将新对象赋给子构造函数的原型对象，所有要将constructor修改回指向字构造函数。
  这种方法区别于用new操作符创建对象赋给子构造函数原型对象完成原型链的方法在于，new构造函数的时构造函数自身的属性与方法
也将赋给子构造函数的原型对象，这样原型对象上就多了父构造函数的方法与属性，只是在访问时，实例对象自身也由于构造函数的
原因也有这些方法与属性，在搜索时不再搜索父构造函数的原型对象而已，明显是多余的。通过直接将父构造函数的原型对象赋给
字构造函数原型对象的方式，避免了多余公用方法和属性的继承。


##闭包
  
*闭包简单点说就是函数中嵌套的函数可以通过作用域链反问外层的变量对象，而外层的无法访问里层变量对象。
  
1. 变量访问遵从作用域链
2. 由于里层引用，可以访问到私有变量与函数
3. 由于引用的存在，可能导致活动对象无法销毁，内存占用过多，需手动设置null销毁
  
**示例4**
<pre><code class="markdown"> 
        function myConstructor(message){
        this.myMessage=message;
        var separator='|';
        var myOwn=this;
        funtion alertMessage(){
        alert(myOwn.myMessage);
        }
        alertMessage();
        this.appendTOMessage=funtion(string){
        this.myMessage+=separator+string;
        alertMessage();
         }
        }
</code></pre>

1. 从上我们可以看到，定义的私有变量separator，myOwn，私有方法alertMessage；但私有方法与属性可以通过将一个方法绑定到实例
对象上，通过方法函数来调用私有变量与方法，这时候其实不是在实例对象上调用，而是通过作用域链的关系引用到了私有方法和属性
由于这种引用的存在使得实例对象间接访问到了私有变量与方法，并且有引用存在的话就不会被销毁。
2. 其中通过将this赋给myOwn，将本作用域的变量对象传到了函数alertMessage，使得可以访问绑定到实例对象上的myMessage；而使用this绑定到实例对象上的方法和属性都不是私有的。

**总结** 闭包最大的作用我想第一产生了私有变量和方法，第二可以通过返回内层函数或将内存函数绑定到实例对象上的方式使得外层函数得以访问到私有变量和方法。

        


          
        
    
    
    
    
  
  
  
