---
layout: post
title: javascript 类的创建与继承
date: 2015.08.09 15:32.000000000 +09:00
category: 前端相关
description: 本文主要针对ES5如何创建类和实现类的继承做了一个总结
tags: 前端;类;OO;ES5;
---

## 创建类

![创建类]({{site.imageurl}}/assets/images/2016/javascript-class-creation-inheritage1.png)<br/>
1、工厂模式：解决了创建多个相似对象的问题，但却没有解决对象识别的问题（即怎样知道一个对象的类型）

```
funtion createPerson(name, age, job) {
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function () {
    alert(this.name);
  };
  return o;
}

var person1 = createPerson("Nicholas", 29, "SE");
```

2、构造函数模式：缺点是每个方法都要在每个实例上重新创建一遍

```
function Person(name, age, job) {
      this.name = name;
      this.age = age;
      this.job = job;
      this.sayName = function() {
        alert(this.name);
      };
 }


var person1 = new Person("Nicholas", 29, "SE");
```

3、原型模式：（1）省略了为构造函数传递初始化参数这一环节，结果所有实例在默认情况下都将取得相同的属性值；（2）原型中的属性是被共享的，这种共享对于函数非常合适。

```
function Person() {
}

Person.prototype = {
  constructor: "Person",
  name: "Nicholas",
  age: 29,
  job: "SE",
  sayName: function() {
    alert(this.name);
  }
};

var person1 = new Person();
person1.sayName();
```

4、构造函数＋原型模式：在ECMAScript中使用最广泛的一种方式
```
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.friends = ["Shelby", "Court"];
}

Person.prototype = {
  constructor: Person,
  sayName: function() {
    alert(this.name);
  }
};

var person1 = new Person("Nicholas", 29, "SE");
```

5、动态原型模式：在其他OO开发中看到独立的构造函数和原型时，很可能会感到非常困惑，此模式就是解决这种困惑的

```
function Person(name, age, job) {
  // 属性
  this.name = name;
  this.age = age;
  this.job = job;

  // 方法
  if (typeof this.sayName != "function") {
    Person.prototype.sayName = function() {
      alert(this.name);
    };
  }
}
```

不能使用对象字面量重写原型。如果在已经创建了实例的情况下重写原型，那么就会切断现有实例与新原型之间的联系。

6、寄生构造函数模式：在前述几种模式都不适用的情况下，可以使用寄生（parasitic）构造函数模式。这种模式的基本思想时创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象；但从表面上看，很像典型的构造函数。

```
function Person(name, age, job) {
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function() {
    alert(this.name);
  };
  return o;
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();// "Nicholas"
```

 除了使用new操作符并把使用的包装函数叫做构造函数外，这个模式跟工厂模式是一样的。
构造函数在不返回值的情况下磨人会返回新对象实例，而通过在构造函数添加return，可以重写调用构造函数时返回的值：

```
function SpecialArray() {
  // 创建数组
  var values = new Array();
  // 添加值
  values.push.apply(values, arguments);
  // 添加方法
  values.toPipedString = function() {
    return this.join("|");
  };
  // 返回数组
  return values;
}
var colors = new SpecialArray("red", "blue", "green");
alert(colors.toPipedString()); // "red|blue|green"
```

关于寄生构造函数模式，返回的对象与构造函数或着与构造函数的原型属性之间没有关系。所以可以使用其他模式的时候，不要使用这种模式。
 
7、稳妥构造函数模式：没有公共属性，而且其方法也不引用this的对象。最适合在一些安全的环境中（禁止使用this和new），或者在防止数据被其他应用程序改动时使用。

```
function Person(name, age, job) {
  // 创建要返回的对象
  var o = new Object();
  
  // 可以在这里定义私有变量和函数

  // 添加方法
  o.sayName = function() {
    alert(name);
  };

  // 返回对象
  return o;
}

// 不要使用new
var friend = Person("Nicholas", 29, "SE");
friend.sayName();
```

与寄生构造函数模有两点不同：1）新创建对象的实例方法不引用this；2）不使用new操作符调用构造函数；

与寄生构造函数模式类似，创建的对象与构造函数之间也没什么关系，因此instanceof操作符对这种对象也没有意义。

## 类的继承

![类继承]({{site.imageurl}}/assets/images/2016/javascript-class-creation-inheritage2.png)<br/>

1、原型链：实现继承的主要方法。

```
function SuperType() {
  this.property = true;
}

SuperType.prototype.getSuperValue = function() {
  return this.property;
};

function SubType() {
  return this.subproperty = false;
}

// 继承了 SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function() {
  return this.subproperty;
};

var instance = new SubType();
alert(instance.getSuperValue());
```

![关系图]({{site.imageurl}}/assets/images/2016/javascript-class-creation-inheritage3.png)<br/>

这个模式有两个问题：（1）共享属性；（2）创建子类型实例时，不能向超类的构造函数中传递参数。或者说没有办法在不影响所有对象实例的情况下，给超类的构造函数换地参数。鉴于有这两个问题，实践中很少会单独使用原型链。

2、借用构造函数：为解决原型中包含引用类型值所带来的问题

```
function SuperType() {
  this.colors = ["red", "blue", "green"];
}

function SubType() {
  // 继承了 SuperType
  SuperType.call(this);
}

var instance1 = new SubType();

```

主要问题是：函数不能复用

3、组合继承：伪经典继承，避免了原型链盒借用构造函数的缺陷，成为最常用的继承模式

```
function SuperType(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function() {
  alert(this.name);
}

function SubType(name, age) {
  SuperType.call(this,name); // 第一次

  this.age = age;
}

SubType.prototype = new SuperType(); // 第二次

SubType.prototype.sayAge = function() {
  alert(this.age);
}

var instance1 = new SubType("Nicholas", 29);
```

最大的问题就是会调两次超类型构造函数。

4、原型式继承：在没有必要兴师动众地创建构造函数，而只想让一个对象与另一个对象保持类似的情况下使用。

```
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}

// 本质上讲，object()对传入其中对对象进行了一次浅复制
var person = {
	name: "Nicholas",
	friends: {"Shelby", "Court", "Van"}
};
var anotherPerson = object(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = object(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

alert(person.friends); // "Shelby,Court,Van,Rob,Barbie"
```

ECMAScript5通过新增Object.create()方法规范了原型式继承。

```
var person = {
           name: "Nicholas",
           friends: ["Shelby", "Court", "Van"]
        };
        
        var anotherPerson = Object.create(person);
        anotherPerson.name = "Greg";
        anotherPerson.friends.push("Rob");
        
        var yetanotherPerson = Object.create(person);
        yetanotherPerson.name = "Linda";
        yetanotherPerson.friends.push("Barbie");
        
        alert(person.friends);// "Shelby,Court,Van,Rob,Barbie"
```

5、寄生式继承：会由于不能做到函数复用而降低效率

```
function createAnother(original) {
  var clone = object(original);  // 创建一个新对象
  clone.sayHi = function() {  // 通过扩展增强该对象
    alert("hi");
  };
  return clone;
}

var person = {
     name: "",
     friends: ["Shelby", "Court", "Van"]
 };
 var anotherPerson = createAnother(person);
 anotherPerson.sayHi(); // "hi"
```

6、寄生组合式继承：只调用一次SuperType构造函数，开发人员普遍认为寄生组合继承是引用类型最理想的继承方式

```
function inheritPrototype(subType, superType) {
  var prototype = object(superType.prototype); // 创建对象
  prototype.constructor = subType;             // 增强对象
  subType.prototype = prototype;               // 指定对象
}

function SuperType(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function() {
  alert(this.name);
};

function SubType(name, age) {
  SuperType.call(this, name);
  this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function() {
  alert(this.age);
};
```
