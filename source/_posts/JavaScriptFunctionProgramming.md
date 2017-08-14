---
title: js函数式编程
tags:
  - js
  - 函数式编程
  - 柯里化
  - 过程抽象
categories:
  - js
  - 原生js

date: 2017-04-13 23:36:45
---

引自：[函数式编程-阮一峰](http://www.ruanyifeng.com/blog/2017/02/fp-tutorial.html)
#### 常见说法
* 与面向对象编程和过程编程并列的编程范式
* 最主要特征：函数式第一等公民
* 强调将计算过程分解成可复用的函数，典型例子就是map方法和reduce方法合成
* 只有纯的、没有副作用的函数，才是合格的函数

上面的说法都是正确的，但是还不够，因为没有说明为什么要这么做

## 1、范畴论

### 1.1 范畴的概念
什么是范畴：彼此之间存在某种关系的概念、失误、对象等，只要能找出他们之间的关系，就能定义一种范畴

范畴论认为：同一个范畴的所有成员，就是不同状态的“变形”，通过“态射”可以变成另一个成员，“态射”正式范畴成员之间的关系

### 1.2 数学模型
既然“范畴”是满足某种变形关系的所有对象，就可以总结出它的数学模型

* 所有成员是一个集合
* 变形关系是函数

就是说，范畴论是集合论的更上层的抽象，简单的理解就是“集合+函数”
理论上通过函数，就可以从范畴的一个成员，计算出所有成员

### 1.3 范畴与容器
我们可以把“范畴”想象成是一个容器，里面包含两样东西

* 值

* 值的变形关系，也就是函数

下面我们使用代码，定义一个简单的范畴
​```js
class Category {
    constructor(val) {
        this.val = val;
    }
    addOne(x) {
        return x + 1;
    }
}
​```
上面代码中，Category是一个类，也是一个容器，里面包含一个值（this.val）和一种变形关系（addOne）。你可能已经看出来了，这里的范畴，就是所有彼此之间相差1的数字。:b::thumbsup:

本文后面的部分，凡是提到“容器”的地方，全部都是指“范畴”

### 1.4 范畴论与函数式编程的关系
范畴论使用函数，表达范畴之间的关系
伴随着范畴论的发展，就发展出一整套函数的运算。这套方案起初只用于数学运算，后来有人将它在计算机上实现了，就变成了今天的“函数式编程”
> 本质上，函数式编程只是范畴论的运算方法，跟数理逻辑、微积分、行列式是同一类东西，都是数学方法，只是碰巧它能用来写程序。

所以，为什么函数式编程必须要求函数是纯的，不能有副作用？因为它是一种数学运算，原是目的就是求值，不做其他事情，否则就无法满足函数运算法则了。

总之，在函数式编程中，函数就是一个管道（pipe）。这头进去一个值，那头就会出来新的值，没有其他作用。

## 二、函数的合成与柯里化
函数式编程有两个最基本的原酸：合成和柯里化。
### 2.1 函数的合成
如果一个值要经过多个函数，才能变成另外一个值，就可以把中间步骤合并成一个函数，这叫做“函数的合成”（compose）
![函数合成图](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017022204.png)
上图中，X和Y之间的变形关系是函数f，Y和Z之间的变形关系是函数g，那么X和Z之间的关系，就是`g`和`f`的合成函数`g·f`

下面就是代码实现，合成两个函数的简单代码如下

​```js
  const compose = function (f, g) {
    return function (x) {
      return f(g(x))
    }
  }
​```
函数的合成还必须满足结合律
![结合律](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017022209.png)
​```js
  compose(f, compose(g, h))
  // 等同于
  compose(compose(f, g), h)
  // 等同于
  compose(f, g, h)
​```
合成也是函数必须是纯的一个原因。因为一个不纯的函数，怎么跟其他函数合成？怎么保证各种合成以后，他会达到预期的行为？

前面说过，函数就像数据的管道（pipe）。那么，函数合成就是将这些管道连起来，让数据一口气从多个管道中穿过。

### 柯里化
`f(x)和g(x)`合成为`f(g(x))`,有一个隐藏的前提，就是`f`和`g`都只能接受一个参数。如果可以接受多个参数，比如`f(x, y)`和`g(a, b, c)`,函数合成就非常麻烦。

这时就需要函数柯里化了。所谓“柯里化”,就是把一个多参函数的函数，转化为单参函数。
​```js
  // 柯里化之前
  function add(x, y){
    return x+y
  }
  add(1,2) // 3

  // 柯里化后
  function addX(y) {
    return function (x) {
      return x+y
    };
  }

  addX(2)(1) // 3
​```
有了柯里化以后，我们就能做到，所有函数只接受一个参数。后文的内容除非另有说明，都默认函数只有一个参数，就是所要出来的那个值。

## 三、函子
函数不仅可以用于同一个范畴之中值的转换，还可以用于将一个范畴转换成另一个范畴。这就涉及到了函子（Functor）
### 3.1 函子的概念
函子是函数式编程里面最重要的数据类型，也是基本的运算单位和功能单位。

它首先是一种范畴，也就是说，是一个容器，包含了值和变形关系。**比较特殊的是，它的变形关系可以一次作用于每一个值，将当前容器变成另一个容器。**
![函子](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017022203.png)
上图中，左侧的圆圈就是一个函子，便是人名的范畴。外部传入函数`f`，会转成右边表示早餐的范畴.
下面是一张更一般的图。
![函子](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017022211.jpg)
上图中，函数`f`完成值的转换（`a`到`b`），将它传入函子，就可以实现范畴的转换(`Fa`到`Fb`)
### 3.2 函子的代码实现
任何具有`map`方法的数据结构，都可以当做函子的实现
​```js
  class Functor {
    constructor(val) {
      this.val = val;
    }
    
    map(f) {
      return new Functor(f(this.val));
    }
  }
​```
上面代码中，`Functor`是一个函子，它的`map`方法接受函数`f`作为参数，然后返回一个新的函子，里面包含的值是被`f`处理过的(`f(this.val)`)。
**一般约定，函子的标志就是容器具有`map`方法。该方法将容器里面的每一个值，映射到另一个容器。**
下面是一些用法的实例
​```js
  (new Functor(2).map(function(two){
    return two+2;
  }));
  // Functor(4)
  (new Functor('flamethrowers')).map(function(s){
    return s.toUpperCase();
  });
  // Functor('FLAMETHROWERS')

  (new Functor('bombs')).map(_.concat(' away')).map(_.prop('length'));
  // Functor(10)
​```
上面的例子说明，函数式编程里面的运算，都是通过函子完成，即运算不直接针对值，而是针对这个值的容器---函子。函子本身具有对外接口（`map`方法），各种函数就是运算符，通过接口接入容器，引发容器里面的值的变形。

因此，**学习函数式编程，实际上就是学习函子的各种运算。**由于可以把运算方法封装在函子里面，所以又衍生出各种不同类型的函子，有多少种运算，就有多少种函子。函数式编程就变成了运用不同的函子，解决问题。

## 四、of方法
你可能注意到了，上面生成新的函子的时候，用了`new`命令。这是在太不像函数式编程了，因为`new`命令是面向对象编程的标志。
**函数式编程一般约定，函子有一个of方法，用来生成新的容器。**
下面就用`of`方法替换掉`new`.
​```js
  Functor.of = function(val) {
    return new Functor(val);
  }
​```
然后，前面的例子就可以改成下面这样。
​```js
  Functor.of(2).map(function (two) {
    return two + 2
  });
  // Functor(4)
​```
这就更像函数式编程了

## 五、Maybe函子
函子接受各种函数，处理容器内部的值。这里就有一个问题，容器内部的值可能是一个空值（比如`null`）,而外部函数未必有处理控制的机制，如果传入控制，很可能出错。
​```js
  Functor.of(null).map(function(s){
    return s.toUpperCase();
  });
  // TypeError
​```
上面代码中，函子里面的值是`null`,结果小写变成大写的时候就出错了。
Maybe函子就是为了解决这一问题而设计的。简单说，它的`map`方法里面设置了控制检查。
​```js
  class Maybe extends Functor {
    map(f) {
      reutrn this.val ? Maybe.of(f(this.val)) : Maybe.of(null);
    }
  }
​```
有了Maybe函子，处理空值就不会出错了
​```js
  Maybe.of(null).map(function(s){
    return s.toUpperCase();
  });
  // Maybe(null)
​```

## 六、Either函子
条件运算`if...else`是最常见的运算之一，函数式编程里面，使用Either汉字表达式。Either函子内部有两个值：左值(`Left`)和右值(`Right`).右值是正常情况下使用的值，左值是右值不存在时使用的默认值。
​```js
  class Either extends Functor {
    constructor(left, right) {
      this.left = left;
      this.right = right;
    }
    
    map(f) {
      return this.right ? 
        Either.of(this.left, f(this.right)) : 
        Either.of(f(this.left), this.right);
    }
  }

  Euther.of = function(left, right) {
    return new Either(left, right);
  };
​```
下面是用法。
​```js
  var addOne = function(x) {
    return x+1;
  };

  Either.of(5, 6).map(addOne);
  // Either(5, 7);

  Either.of(1, null).map(addOne);
  // Either(2, null);
​```
上面代码中，如果右值有值，就是用右值，否则使用左值。通过这种方式，Either函子表达了条件运算。
Either函子的常见用途是提供默认值。下面是一个例子
​```js
  Either
  .of({address: 'xxx'}, currentUser.address)
  .map(updateField);
​```
上面代码中，如果用户没有提供地址，Either函子就会使用左值的默认值。
Either函子的另一个用途是代替`try...catch`, 使用左值表示错误。
​```js
  function parseJSON(json) {
    try {
      return Either.of(null, JSON.parse(json));
    } catch (e: Error) {
      return Either.of(e, null);
    }
  }
​```
上面代码中，左值为空，就表示没有出错，否则左值会包含一个错误对象`e`.一般来说，所有可能搓搓的运算，都可以返回一个Either函子。

## 七、ap函子
汉字里面包含的值，完全可能是函数。我们可以想象这样一种情况，一个函子的值是数值，另一个函子的值是函数。
​```js
  function addTwo(x) {
    return x+2;
  }

  const A = Functor.of(2);
  const B = Functor.of(addTwo);
​```
上面代码中，函子`A`内部的值是`2`, 函子`B`内部的值是函数`addTwo`.
有时，我们想让函子`B`内部的函数，可以使用函子`A`内部的值进行运算。这是就需要用到ap函子。
ap是application的缩写。凡是部署了`ap`方法的函子，就是ap函子。
​```js
  class Ap extends Functor {
    ap(F) {
      return Ap.of(this.val(F.val));
    }
  }
​```
注意，`ap`方法的参数不是函数，而是另一种函子。
因此，前面例子可以写成下面的形式
​```js
  Ap.of(addTwo).ap(Functor.of(2))
  // Ap(4)
​```
ap函子的意义在于，对于那些多参数的函数，就可以从多个容器中取值，实现函子的链式操作
​```js
  function add(x) {
    return function(y) {
      return x+y
    }
  }
  Ap.of(add).ap(Maybe.of(2)).ap(Maybe.of(3));
​```
上面代码中，函数`add`是柯里化以后的形式，一共需要两个参数。通过ap函子，我们就可以实现从两个容器之中取值。它还有另一种写法
​```js
  Ap.of(add(2)).ap(Maybe.of(3));
​```

## 八、Monad函子
函子是一个容器，可以包含任何值。函子之中在包含一个函子，也是完全合法的。但是，这样就会出现多层嵌套的函子。

```js

```

  Maybe.of(

    Maybe.of(
      Maybe.of({name: 'Mulburry', number: 8402})
    )
  )
​```
上面这个函子，一共有三个`Maybe`嵌套。如果要去除内部的值，就要连续取三次`this.val`.这当然很不方便，因此就出现了Monad函子。
**Monad函子的作用是，总返回一个单层的函子。**它有一个`flatMap`方法，与`map`方法作用相同，唯一的区别是如果生成了一个嵌套函子，它会去除后者内部的值，保证反悔的永远是一个单层的容器，不会出现潜逃的情况。
​```js
  class Monad extends Funtor {
    join() {
      return this.val;
    }
    flatMap(f) {
      return this.map(f).join();
    }
  }
​```
上面代码中，如果函数`f`返回的是一个函子，那么`this.map(f)`就会生成一个嵌套的函子。所以，`join`方法保证了`flatNap`方法总是返回一个单层的函子。这意味着嵌套的函子会被铺平（flattern）

## 九、IO操作
Monad函子的重要应用就是实现I/O操作
I/O是不纯的操作，普通的函数式编程没法做，这时就需要把IO操作写成`Monad`函子，通过它来完成
​```js
  var fs = require('fs');
  
  var readFile = function(fileName) {
    return new IO(function(){
      return fs.readFileSync(fileName, 'utf-8');
    });
  };
  
  var print = function(x) {
    return new IO(function(){
      console.log(x);
      return x;
    });
  }
​```
上面代码中，读取文件和打印文件本身都是不纯的操作，但是`readFile`和`print`是纯函数，因为他们总是返回IO函子。
如果IO函子是一个`Monad`,具有`flatMap`方法，那么我们就可以像下面这样调用这两个函数。
​```js
  readFile('./user.text')
  .flatMap(print)
​```
上面的代码完成了不纯的操作，但是因为`flatMap`返回的还是一个IO函子，所以这个表达式是纯的。我们通过一个纯的表达式，完成带有副作用的操作，这就是Monad的作用。
由于返回的还是IO函子，所以可以实现链式操作。因此，在大多数库里面，`flatMap`方法被该名成`chain`.
​```js
  var tail = function(x){
    return new IO(function(){
      return x[x.length-1];
    });
  }
  
  readFile('./user.txt)
  .flatMap(tail)
  .flatMap(print)
  
  // 等同于
  readFile('./user.txt)
  .chain(tail)
  .chain(tail)
​```
上面代码读取了文件`user.txt`,然后选取最后一行打印

## 十、参考链接
* [JS函数式编程指南](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/)
* [函数与函数式编程](http://www.jianshu.com/p/69dede6f7e5f)
* [柯里化](http://www.jianshu.com/p/5e1899fe7d6b)































