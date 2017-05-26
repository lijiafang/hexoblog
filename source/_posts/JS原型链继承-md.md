---
title: JS原型链继承.md
date: 2017-04-18 19:17:04
tags: JS
---
JS是基于对象的，所有对象都是基于Object，都继承了Object.prototype的属性和方法,Object.prototype.__proto__指向null

## 基本概念 

语法中没有类，可以使用原型链实现继承，这里涉及到几个概念

*   封装：使用构造函数将一组属性和方法封装起来

*   构造函数：普通的函数，内部使用this变量，this指向实例

*   new运算符：通过构造函数创建实例对象，并且将this变量绑定到实例对象

*   prototype：
原型对象，只有函数对象有该属性，其他类型的对象没有；该特性需要通过prototype存在的意义理解，为了通过构造函数实现继承而有的该对象，构造函数也是函数的一种，所以只有函数对象有这个对象。构造函数生成实例，生成的每个实例都拥有自己的私有属性、方法（放在构造函数中的），不能共享属性、方法，为解决这一问题，引入原型对象；

*   原型链继承：
    给构造函数加上prototype对象，把需要共享的属性、方法放入这个对象中，实例在创建时，会有__proto__属性，该属性指向构造函数的prototype对象，从而继承原型对象中的属性、方法

*   prototype、__proto__、constructor的区别与关联
    prototype：原型对象，只有函数对象才会有
constructor：始终指向该对象的构造函数，是每个具有原型（__proto__）的对象的原型成员,这包括所有内部 JavaScript 对象,   constructor 属性包含了对某种函数的引用，此种函数构造了特定对象的实例
构造函数.prototype.constructor == 构造函数
__proto__：每个对象都会有，因为所有对象都是继承自Object.prototype，都是一个对象实例

### 检测对象属性是否私有
* hasOwnProperty: 
验证对象的属性是私有的还是继承自prototype的     
使用方法：
```
    dogA.hasOwnProperty("name")
```

* in：
遍历对象的所有属性，包括本地的和继承的，
使用方法
```
     for(var item in dogA){
        console.log(item)
     }
```

# 继承的方法：
通过构造函数的继承[参考链接](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)

* 构造函数绑定：
    在子对象的构造函数里绑定父对象的构造函数，这种将父对象构造函数中属性和方法放入到子构函数中，创建实例时将作为实例的私有属性方法,这种继承方法不会继承父对象构造函数的prototype对象，
    只会继承父对象构造函数的非共享属性方法，继承可变的部分（放在构造函数中的属性方法）
    ParentObj.call(this);


* 使用prototype属性
    子对象构造函数.prototype =new  父对象构造函数()
    子对象构造函数.prototype.constructor = 子对象构造函数（为了防止原型链的混乱，必须将子对象的constructor指回去）
    这种方法，将继承父对象中可变的部分+不变的部分，但是需要额外建了实例

* 直接继承prototype
    子对象构造函数.prototype = 父对象构造函数.prototype
    子对象构造函数.prototype.constructor = 子对象构造函数（为了防止原型链的混乱，必须将子对象的constructor指回去）
    这种方式只继承了父对象构造函数中不变的部分的部分，
    与前一种方法相比，这样做的优点是效率比较高（不用执行和建立Animal的实例了），比较省内存。缺点是 Cat.prototype和Animal.prototype现在指向了同一个对象，那么任何对Cat.prototype的修改，都会反映到父对象prototype

* 结合前两种方法，用空对象做中介

* 拷贝继承，将父对象.prototype中的属行方法一一拷贝到子对象.prototype

* 通过非构造函数的继承
    [参考链接](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance_continued.html)

## 分析活动后台组件之间的继承方法：
### 基本概念：

__proto__:
    所有的对象都具有这个属性，js中一切皆对象，就是所有的东西都有这个属性，隐式原型指向该对象构造函数的原型对象，保证了实例能够访问到在构造函数的原型对象上定义的属性方法

prototype: 
    原型对象，函数对象还有自己的特殊属性prototype，该属性指向该方法的原型对象

constructor:
    原型对象有该属性，指向构造函数

### 组件实现过程中用到的继承方法
基础组件BaseModel,

构造函数绑定：
```
    BaseModel.apply(this, arguments);
```

将BaseModel的构造函数中的私有属性方法放入子组件的构造函数中

    inherit(constructor, sub, superClass)方法中
        agent = {
             constructor: constructor,
             sub中的属性方法复制过来,
             __proto__: superClass.prototype
        }
    constructor.prototype = 
    {
         constructor: constructor,
         sub中的属性方法复制过来,
         __proto__: superClass.prototype || {}
    }
