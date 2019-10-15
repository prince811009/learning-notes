---
title: JavaScript_prototype chain -2
date: 2019-09-13 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
---

第一回我們了解完 prototype 出現的前因後果，後面我們接續著去探索一下當我們下這些指令的時候， JavaScript 在背後做了哪些事情呢?

### JavaScript 在背後默默做的事
我們想知道的是，當我們呼叫這程式碼的時候，JavaScript 要如何循著線索找到我們要的答案 ?

```
var dogA = new Dog('Lucky');
```

<!-- more -->

從這幾行中我們還無法看到這個 instance 本身內的 function，但是根據 JavaScript 的機制，如果 dogA 是 Dog 的 instance，就能夠從 `Dog.prototype` 中尋找屬性或方法。

所以有趣的是，在 DogA 和 `Dog.prototype` 之間一定有某些關聯或是方式把他們之間串聯在一起，這樣 JavaScript 才能循著找到原型，而這個連接方式也就是 `__proto__` ( 兩個底線 )，可參閱 [Object.prototype.__proto__](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto) 。

- example

    ```
    function Dog(name, age) {
        this.name = name;
        this.age = age;
    }

    Dog.prototype.log = function () {
        console.log(this.name + ', age:' + this.age);
    }

    var dogA = new Dog('Lucky', 5);
    console.log(dogA.__proto__ === Dog.prototype) // true
    ```

    在這個例子中， `dogA.__proto__` 會指向 `Dog.prototype` ，所以就算原本的 instance 沒有看到這些 methods ，但是 JavaScript 仍然可以循著這個路徑去找到  `Dog.prototype` ，以及其中的 methods 。

    那有可能會有找不到的情況嗎 ? 那這時候 JavaScript 就會再往下一層  `Dog.prototype.__proto__` 去尋找，一直到找到為止 ( 或是找到 null 為止 )，而這項透過 `__proto` 一直循線找下去的現象稱為 "prototype chain" ( 原型鍊 )，而這就達成了 JavaScript 想要做出類似繼承的功能，可以呼叫自己 parent 的屬性和 methods 。

    所以後面我們就來試試如果一直用 `__proto` 找下去的話會找到什麼呢 ?

    ```
    function Dog(name, age) {
        this.name = name;
        this.age = age;
    }

    Dog.prototype.log = function () {
        console.log(this.name + ', age:' + this.age);
    }

    var dogA = new Dog('Lucky', 5);
    console.log(dogA.__proto__ === Dog.prototype) // true
    // 以上為剛剛上方的 example 

    //keep searching
    console.log(Dog.prototype.__proto__ === Object.prototype) // true
    console.log(Object.prototype.__proto__) // null, the top of the prototype chain
    ```
---
### 你的屬性不是你的屬性 ? 
既然我們知道 instance 可以有類似繼承 parent 的屬性，那我們又要如何區別某項屬性是 instance 原本就有的，或是他是屬於  `prototype` 裡面的 ?

#### hasOwnProperty

`hasOwnProperty` 正好提供了這項功能，讓我們直接看範例：

```
function Dog(name, age) {
    this.name = name;
    this.age = age;
}

Dog.prototype.log = function () {
    console.log(this.name + ', age:' + this.age);
}

var dogA = new Dog('Lucky', 5);
console.log(dogA.hasOwnProperty('log')); // false
console.log(dogA.__proto__.hasOwnProperty('log')); // true
```


`hasOwnProperty` 是不是很方便檢查用呢 ? 

- quiz:

    ```
    function Dog(name, age) {
        this.name = name;
        this.age = age;
    }

    Dog.prototype.log = function () {
        console.log(this.name + ', age:' + this.age);
    }

    var dogA = new Dog('Lucky', 5);
    console.log(Dog.__proto__ === Function.prototype); // true
    console.log(Function.prototype.__proto__ === Object.prototype); //true
    console.log(Object.prototype.__proto__); //null
    ```
---
### instanceof
用來判斷 A 是否為 B 的 instance ( 用法： `A instanceof B` )。

```
function Dog(name, age) {
    this.name = name;
    this.age = age;
}

Dog.prototype.log = function () {
    console.log(this.name + ', age:' + this.age);
}

var dogA = new Dog('Lucky', 5);

console.log(dogA instanceof Dog); // true
console.log(dogA instanceof Object); // true
console.log(dogA instanceof Array); // false
```
---
### constructor
暨上一回有提到 `constructor` ，每一個 prototype 都有一個叫做 `constructor` 的屬性
- example
 `Dog.prototype.constructor`
 
    而這個屬性會指向構造函數，也就是 `Dog` ( 好像繞口令 ~)。
- example

```
Dog.prototype 的建構函數也就是 Dog 本身
```
---
### new 
#### 定義
`new` 這個指令是讓我們在具有 constructor 的 function 中創造一個 instance，當我們下 `new` 這個指令後，會依序進行：
1. Creates a blank, plain JavaScript object;
2. Links (sets the constructor of) this object to another object;
3. Passes the newly created object from Step 1 as the this context;
4. Returns this if the function doesn't return its own object.

讓我們先來試看看如何 `new operator`

    ```
    function Car(make, model, year) {
        this.make = make
        this.model = model
        this.year = year
    }

    var car1 = new Car('Eagle', 'Talon Tsi', 1993)

    console.log(car1.make) // 'Eagle'
    ```

#### 語法

```
new constructor[([arguments])]
```

- constructor : 

    A class or function that specifies the type of the object instance
 
- arguments : 

    A list of values that the constructor will be called with


因此回應到我們上面的例子中，當我們下了這項指令後，表示：

```
var dogA = new Dog('Lucky');
```

JavaScript 會幫我們做以下這些事：
1. 創造出一個新的 Object 
2. 把這個 Object.__proto__ 指向 `Dog.prototype` ，形成 `prototype chain`
3. 把 Object 當作 context ，呼叫 `constructor` ( 也就是 Dog)
4. 回傳 Object
---
### 結論
簡單地說， JavaScript 就是透過 prototype chain 的機制，把各物件的上下關係串聯起來，可以從我們要找的 obj 的 parent ( `obj.__proto__` ) 去循線蹤跡，一直到 the top of the prototype chain ( `Object.prototype` ) ，並一直到 null 結束。


