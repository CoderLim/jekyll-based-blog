---
layout: post
title: this绑定的对象到底指的是哪个？
date: 2015.01.20 09:30.000000000 +09:00
category: 前端相关
description: 全面解析this关键字，什么是this？ this绑定的对象到底指的是哪个？
tags: 前端;javascript;this;
---

> 通过绑定规则来确定this的绑定对象。


## 默认绑定

默认绑定规则看作是无法应用其他规则时的规则，
独立函数调用是最常用的函数调用方式。比如：

```
function foo() {
  console.log( this.a );
}

var a = 2;
foo(); // 2
```

此时foo在调用的时候是**默认绑定**，因为没有指定上下文，所以默认上下文
是window，而`var a = 2;`这句意思是给window添加了一个a属性，并赋值为`2`，
所以会打印`2`。

如果使用**严格模式(strict mode)**，那么全局对象将无法使用默认绑定，因为
this会绑定到**undefined**.

## 隐式绑定

另一条需要考虑的规则是调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含，
不过这种说法可能会造成一些误导。

```
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2,
  foo: foo
};

obj.foo(); // 2
```

当函数引用有上下文对象时，**隐式绑定**规则会把函数调用中的this绑定到这个上下文对象。

对象属性引用链中只有最顶层或者说最后一层会影响调用位置。比如：

```
function foo() {
  console.log( this.a );
}

var obj2 = {
  a: 42,
  foo: foo,
};

var obj1 = {
  a: 2,
  obj2: obj2,
};

obj1.obj2.foo(); // 42
```

### 隐式丢失

一个最常见的this绑定问题就是被**隐式绑定**的函数丢失绑定对象，也就是说它会应用**默认绑定**，
从而把**this**绑定到全局对象或者**undefined**上，取决于是否是严格模式。

```
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2,
  foo: foo,
};

var bar = obj.foo;

var a = "global";

bar(); // "global";
```

虽然bar是obj.foo的一个引用，但是实际上，它引用的是foo函数本身，因此此时的bar()实际是一个
不带任何修饰的函数调用，因此是默认绑定。

一种更微妙、更常见并且更出乎意料的情况发生在传入回调函数时：

```
function foo() {
  console.log( this. a );
}

function doFoo(fn) {
  fn(); 
}

var obj = {
  a: 2,
  foo: foo,
};

var a = "global";// 全局对象的属性

doFoo( obj.foo ); // "global"
```

## 显式绑定

涉及到的是**call**和**apply**，它们额第一个参数是一个对象，它们会把这个对象绑定到this，
接着在调用函数时指定这个this。因为你可以直接指定this的绑定对象，所以称之为显示绑定。

```
function foo() {
  console.log( this. a );
}

var obj = {
  a: 2
};

foo.call( obj ); // 2
```

如果你传入了一个原始值（字符串类型、布尔类型或者数字类型）来当作this的绑定对象，这个原始值
会被“装箱”，也就是(new String(...)、new Boolean(...)或者new Number(...))。

可惜，**显示绑定**仍然无法解决我们之前提出的丢失绑定问题。

### 硬绑定

但是硬绑定的一个变种可以解决，如下：

```
function foo() {
  console.log( this. a );
}

var obj = {
  a: 2,
};

var bar = function() {
  foo.call( obj );
};

bar(); // 2
setTimeout( bar, 100 ); // 2

// 硬绑定的bar 不能再修改它的this
bar.call( window ); // 2
```

硬绑定的典型应用场景就是创建一个包裹函数，传入所有的参数并返回接收到的所有值：

```
function foo(something) {
  console.log( this.a, something );
  return this.a + something;
}

var obj = {
  a: 2
};

var bar = function() {
  return foo.apply( obj, arguments );
};

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

由于**硬绑定**是一种非常常用的模式，所以在ES5中提供了内置的方法Function.prototype.bind, 用法如下：

```
function foo(something) {
  console.log( this.a, something );
  return this.a + something;
}

var obj = {
  a: 2
};

var bar = foo.bind( obj );

var b = bar( 3 ); // 2 3
console.log( b ); //5
```

### API调用的“上下文”

第三方库的许多函数，以及js语言和宿主环境中许多新的内置函数，都提供了一个可选的参数，通常被成为“上下文”，比如：

```
function foo(el) {
  console.log( el, this.id );
}

var obj = {
  id: "awesome"
};

[1,2,3].forEach( foo, obj );
// 1 awesome 2 awesome 3awesome
```

## new绑定

这是最后一条规则。

```
function foo(a) {
  this.a = a;
}

var bar = new foo(2);
console.log( bar.a ); // 2
```

## 优先级

- 隐式绑定 VS 显示绑定

```
function foo() {
  console.log( this.a );
}

var obj1 = {
  a: 2,
  foo: foo
};

var obj2 = {
  a: 3,
  foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call( obj2 ); // 3
obj2.foo.call( obj1 ); // 2
```

可以看到，显示绑定优先级更高。

- new 绑定 VS 隐式绑定

```
function foo(something) {
  this.a = something;
}

var obj1 = {
  foo: foo,
};

var obj2 = {};

obj1.foo( 2 );
console.log( obj1.a ); // 2

obj1.foo.call( obj2, 3 );
console.log( obj2.a ); // 3

var bar = new obj1.foo( 4 );
console.log( obj1.a ); // 2
console.log( bar.a ); // 4
```

可以看到new优先级比隐式绑定高。那么new绑定和显式绑定哪个优先级高呢？

在看代码之前先回忆一下硬绑定是如何工作的。Function.prototype.bind(..)会创建一个新的包装函数，
这个函数会忽略它当前的this绑定（无论绑定的对象是什么），并把我们提供的对象绑定到this上。

这样看起来硬绑定（也是显式绑定的一种）似乎比new绑定的优先级更高，无法使用new来控制this绑定。

我们看看是不是这样：

```
function foo(something) {
  this.a = something;
}

var obj1 = {};

var bar = foo.bind( obj1 );
bar( 2 );
console.log( obj1.a ); // 2

var baz = new bar(3);
console.log( obj1.a ); // 2
console.log( baz.a ); // 3
```

奇怪，bar被硬绑定到obj1上，但是new bar(3)并没有像我们预计的那样把obj1.a修改为3。相反，new修改了硬绑定调用bar(..)中的this。
因为使用了new绑定，我们得到了一个名字为baz的新对象，并且baz.a的值是3。

再来看看我们直接介绍的"裸"辅助函数bind：

```
function bind(fn, obj) {
  return function() {
    fn.apply( obj, arguments );
  };
}
```

非常令人惊讶，因为看起来在辅助函数中new操作符的调用无法修改this绑定。

实际上，ES5中内置的Function.prototype.bind(..)更复杂。下面是MDN提供的实现：

```
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) {
    if (typeof this !== 'function') {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
    }

    var aArgs   = Array.prototype.slice.call(arguments, 1),
        fToBind = this,
        fNOP    = function() {},
        fBound  = function() {
          return fToBind.apply(this instanceof fNOP
                 ? this
                 : oThis,
                 aArgs.concat(Array.prototype.slice.call(arguments)));
        };

    if (this.prototype) {
      // Function.prototype doesn't have a prototype property
      fNOP.prototype = this.prototype; 
    }
    fBound.prototype = new fNOP();

    return fBound;
  };
}
```

下面是new修改this的相关代码：

```
this instanceof fNOP ? this : oThis 
以及
fNOP.prototype = this.prototype;
fBound.prototype = new fNOP();
```

这段代码会判断硬绑定函数是不是被new调用，如果是就会使用新创建的this替换硬绑定的this。

那么，为什么要在new中使用应绑定函数呢？直接使用普通函数更简单吗？

之所以要在new中使用硬绑定函数，主要目的是预先设置函数的一些参数，这样在使用new进行
初始化时就可以只穿传入其余参数。bind(..)的功能之一就是可以把除了第一个参数（第一个
参数用于绑定this）之外的其他参数都传给下层的函数（这种技术成为“部分应用”， 是“柯里化”的一种）

举例来说：

```
function foo(p1, p2) {
  this.val = p1 + p2;
}

var bar = foo.bind( null, "p1" );

var baz = new bar( "p2" );

baz.val; // p1p2
```


## 参考

[你不懂的javascript(上卷)]()
