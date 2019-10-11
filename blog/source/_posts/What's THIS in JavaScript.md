---
title: What's THIS in JavaScript ?
date: 2019-09-25 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/What's%20THIS%20in%20JavaScript.md
---

### 前言
常常出現以及使用的 ```this``` 似乎是一個常見又難纏的敵人，以下整理及提供一些資料參考。

### 由物件導向的角度開始探討

試宣告了一個 class ```Car``` ，我們可以使用兩個 methods ( setName and getName )，並在裡面使用 ```this.name``` 存取 instance 的屬性，由此可見這裡的 ```this``` 指的對象就是 instance 本身 ( ```myCar``` )。


<!-- more -->

```
class Car {
    setName(name) {
        this.name = name
    }
   
    getName() {
        return this.name
    }
}
       
const myCar = new Car()
myCar.setName('hello')
console.log(myCar.getName()) // hello
```
### 物件導向之外情況
 - 嚴格模式下 => undefined
 - 非嚴格模式，且在瀏覽器下執行 => window
 - 非嚴格模式，且在 node.js 下執行 => global

### 更改 this 值

儘管 this 可能有預設值，但仍可透過一些方法去修改：
```
'use strict';
function hello(a, b){
    console.log(this, a, b)
}
   
hello(1, 2) // undefined 1 2
hello.call(undefined, 1, 2) // undefined 1 2
hello.apply(undefined, [1, 2]) // undefined 1 2
```
    
 - 直接呼叫 function 
     *  上述程式碼可以從 function ```hello``` 可以 log 出 ```this``` 的值和 a, b 兩個參數，因此在呼叫 ```hello(1, 2)``` 的時候，在嚴格模式下 ```this``` 會是 ```undefined``` ， 而 a, b 分別為 1, 2。

 - 使用 ```call``` 和 ```apply``` 方式呼叫 function。

     *  當使用 ```hello.call(undefined, 1, 2)``` 的時候，會發現回傳結果和呼叫 ```hello(1, 2)``` 相同。

     *  若使用 ```hello.apply(undefined, [1, 2])``` 回傳值也是相同，差別部分在於傳進去的參數需要是 array 。 

     *  回到上述未解的問題，也就是一直為 ```undefined``` 的那項參數 => ```this``` 的值，以下舉例說明：
        - example 01 :
            ```
            'use strict';
            function hello(a, b){
                console.log(this, a, b)
            }

            hello.call('yo', 1, 2) // yo 1 2
            hello.apply('hihihi', [1, 2]) // hihihi 1 2
            ```
            有趣的是，傳進的的參數為何，```this``` 的值就會是那項參數，覆蓋過預設的 ```this``` 值。

        - example 02 :
            ```
            class Car {
                hello() {
                    console.log(this)
                }
            }

            const myCar = new Car()
            myCar.hello() // myCar instance
            myCar.hello.call('yoyoyo') // yoyoyo
            ```
            從上述程式碼可以得知，原本預設的 ```this``` 值為當我們呼叫 ```myCar.hello()``` 時回傳的 ```myCar``` 這項 instance，不過當我們使用 ```call``` 呼叫 function 時 ( ```myCar.hello.call('yoyoyo') // yoyoyo``` ) 所傳入的參數所覆蓋掉。 

 - 使用 ```bind``` 方式呼叫 function：
     *  ```bind``` 會回傳一個新的 function ，因此我們將 function ```hello``` 使用 my 綁定，最後呼叫 myHello() 時會輸出 my 。
        ```
        'use strict';
        function hello() {
            console.log(this)
        }

        const myHello = hello.bind('my')
        myHello() // my
        ```

 - 混用 ```call``` / ```apply``` and ```bind``` ：

    這邊可以發現，如果已經使用了 ```bind``` 之後 ```this``` 的值就不會再被改變了。
    ```
    'use strict';
    function hello() {
        console.log(this)
    }

    const myHello = hello.bind('my')
    myHello.call('your') // my
    ```
### 物件中的 this
除了物件導向 class 中的 this ，接續探討一般 JavaScript 中的 object 的情況。
首先先宣告一項物件：
```
const obj = {
    value: 1,
    hello: function() {
        console.log(this.value)
    }
}

obj.hello() // 1
```
這邊所強調的重點為：
> ```this``` 的值與作用域或程式碼順序 / 位置無關，而是和如何呼叫有關。

```this``` 的值會根據使用者如何呼叫 function 而不同，就如同上述所提的三種方式： ```call```, ```apply``` and ```bind``` ，因此也就可以使用不同的方式呼叫 function 使 ```this``` 值有所不同。
 - Step 1 :
    ```
    const obj = {
        value: 1,
        hello: function() {
            console.log(this.value)
        }
    }

    obj.hello() // 1
    const hey = obj.hello
    hey() // undefined
    ```
    可將上面這段程式碼的 function call 轉成使用 ```call``` 的形式來看：
    ```
    const obj = {
        value: 1,
        hello: function() {
            console.log(this.value)
        }
    }

    obj.hello() // 1
    obj.hello.call(obj) // 轉成 call
    const hey = obj.hello
    hey() // undefined
    hey.call() // 轉成 call
    ```
    此時 ```obj.hello()``` 變成 ```obj.hello.call(obj)``` ，而 ```hey()``` 前面沒有東西呼叫，因此變成 ```hey.call()``` 。

 - Step 2 :
    ```
    const obj = {
        value: 1,
        hello: function() {
            console.log(this.value)
        },
        inner: {
            value: 2,
            hello: function() {
                console.log(this.value)
            }
        }
    }

    const obj2 = obj.inner
    const hello = obj.inner.hello
    obj.inner.hello()
    obj2.hello()
    hello()
    ```
    可把最後面這段想成
    ```
    obj.inner.hello() // obj.inner.hello.call(obj.inner) => 2
    obj2.hello() // obj2.hello.call(obj2) => 2
    hello() // hello.call() => undefined ( 非嚴格模式下 -> window.value => undefined)
    ```
 - question 01 :
    ```
    function hello() {
        console.log(this)
    }

    var a = {
        value: 1,
        hello
    }

    var b = {
        value: 2,
        hello
    }

    hello()
    a.hello()
    b.hello.apply(a)
    ```
     * answer :
     
       用 ```call``` 來轉換形式：
     ```
     hello() // hello.call() => window
     a.hello() // a.hello.call(a) => a
     b.hello.apply(a) // 因為使用了 ```apply``` => a
     ```
 - question 02 : 
    ```
    var x = 10
    var obj = {
        x: 20,
        fn: function() {
            var test = function() {
                console.log(this.x)
            }
            test()
        }
    }

    obj.fn()
    ```
     * answer : 

     使用 ```call``` 轉換形式：
     ```
     test() // 轉換成 test.call() -> this 的值為 window，因此 this.x => 10 
     ```
---
### 在 ES6 的 Arrow Function ( 箭頭函式 ) 中
在 ES6 要注意的是：

> 變數宣告的地方的 ```this``` 就是變數的 ```this``` 。

 - example : 

    在 function ```hello``` 中宣告 arrow function ```test``` ，
    ```
    const obj = {
        x: 1,
        hello: function() {
            console.log(this) // 這邊的 ```this``` 就會是 test 的 this
            const test = () => {
                console.log(this.x)
            }
            test()
        }
    }

    obj.hello() // 1
    const hello = obj.hello
    hello() // undefined
    ```
    所以我們可以發現， ```test``` 這個箭頭函式是在 ```hello``` 這個 function 裡面被宣告的，因此 ```test``` 這項 function 所印出的 ```this``` 就會一樣是 ```hello``` 的 ```this``` 的值。

    因此 ```obj.hello()``` 所 log 出來的 ```this``` 是 ```obj``` ，因此 ```test``` 的 ```this``` 的值也就是 ```obj``` 。

    而呼叫 ```hello()``` 時， ```test``` 的 ```this``` 為全域物件，所以是 ```undefined``` 。

---
##### Reference
 - [淺談 JavaScript 頭號難題 this：絕對不完整，但保證好懂](https://github.com/aszx87410/blog/issues/39)
 - [What's THIS in JavaScript ?](https://kuro.tw/posts/2017/10/12/What-is-THIS-in-JavaScript-%E4%B8%8A/)