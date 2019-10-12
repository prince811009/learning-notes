---
title: Closure - about closure and scope in JavaScript
date: 2019-10-1 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/Closure%20-%20about%20closure%20and%20scope%20in%20JavaScript.md
---
### Introduction
首先先簡介 Closure 的特性

<!-- more -->

- example 01 :
    ```
    function test() {
        var a = 10
        function inner() {
            console.log(a) // 10
        }
        inner()
    }

    test()
    ```

    由這項 function，試著改寫成 => 不要直接執行 inner ，而是把這整個 function 直接回傳，會變成：

    ```
    function test() {
        var a = 10
        function inner() {
            console.log(a) // 仍為 10
        }
        return inner // 注意：並非 return inner()
    }

    var inner = test()
    inner()
    ```

    這時因為 `return inner` 的關係，使變數 `a` 也存在於 function inner 之中，所以可以將「在 function 之中 return 一個 function」作為 `Closure` 現象。

    一項重要的優點為，可將變數隱藏在 function 內部，不使外部存取到這項變數，也就無法被隨意變更，如以下的例子：

- example 02 :

    ```
    var myWallet = 100
    function deduct(n) {
        myWallet -= (n > 10 ? 10 : n)
    }

    deduct(13) // 90
    myWallet -= 999 // -909
    ```


    原本變數在 function 內部中特定條件下執行特定的事情，但仍能被外部存取且修改，若利用 closure　改寫，就能夠避免這項問題。

    ```
    function getWallet() {
        var myWallet = 100
        return {
            deduct: function(n) {
                myWallet -= (n > 10 ? 10 : n)
            }
        }
    }

    var wallet = getWallet()
    wallet.deduct(13) // 90
    myWallet -= 999 // Uncaught ReferenceError: my_balance is not defined
    ```

    上述例子出現錯誤的原因為，因為變數被隱藏在 function 內部，因此外部無法存取到，若需要修改需透過執行 `deduct` 這項 function，達到隱藏資訊的目的，變數不會被隨意更改。

- example 03 : 
    另一項常見的例子 

    ```
    var arr = []
    for (var i = 0; i < 4; i++) {
        arr[i] = function() {
            console.log(i) // 4
        }
    }

    arr[0]()
    ```

    原因為當我們呼叫 `arr[0]()` 時，程式會去尋找這詞變數 `i` 為何，但是這時是迴圈已經全部跑完跳出時產生的 `i` ，因為 function 本身沒有 `i` 這項變數，因此往作用域的外層尋找時，就是找到這項跑完迴圈的 `i` ，因此 `i` 為 4 。

    若要解決這項問題，可使用幾種方式：
    
     *  `IIFE（Immediately Invoked Function Expression )`

        可以將一個 function 包起來並把 `i` 立即傳給程式執行，因此迴圈每跑一圈就會立刻呼叫一個新的 function ，也就是新產生一個新的作用域。

        ```
        var arr = []
        for (var i = 0; i < 4; i++) {
            arr[i] = (function(num) {
                return function() {
                    console.log(num)
                }
            })(i)
        }

        arr[0]()
        ```

    *  `let` 
    
        使用 ES6 語法
        ```
        var arr = []
        for (let i = 0; i < 4; i++) {
            arr[i] = function() {
                console.log(i) // 4
            }
        }

        arr[0]()
        ```
---
### 從 ECMAScript 中探討 scope
- `10.1.4 Scope Chain and Identifier Resolution`
    > Every execution context has associated with it a scope chain. A scope chain is a list of objects that are searched when evaluating an Identifier. When control enters an execution context, a scope chain is created and populated with an initial set of objects, depending on the type of code.

    每個 EC 都有屬於自己的 scope chain，當進入 EC 時 scope chain 就會被建立。

- `10.2 Entering An Execution Context`
   *  `10.2.3 Function Code`
        > The scope chain is initialised to contain the activation object followed by the objects in the scope chain stored in the [[Scope]] property of the Function object.

        當進入 EC 時，scope chain 會被初始化為 activation object，並加上 function 的 [[Scope]] 屬性。
        ```
        scope chain = AO + [[Scope]]
        ```
- `13.2 Creating Function Objects`
    > Given an optional parameter list specified by FormalParameterList, a body specified by FunctionBody, and a scope chain specified by Scope, a Function object is constructed as follows:  
    ...  
    > 7. Set the [[Scope]] property of F to a new scope chain (10.1.4) that contains the same objects as Scope.
    
    當我們建立 function 時會設定的 [[Scope]] ，裡面內含 scope 。
---
### 探討 closure 行程過程及原理
依據上述方式一步一步拆解過程
- example 
    ```
    var v1 = 10
    function test() {
        var vTest = 20
        function inner() {
            console.log(v1, vTest) // 10, 20
        }
        return inner
    }
    var inner = test()
    inner()
    ```

 1. 進入 global EC 

    進入 global EC，並初始化 VO and scope chain。
    ```
    globalEC {
        VO: {
            v1: undefined,
            inner: undefined,
            test: func
        },
        scopeChain: globalEC.VO
    }
    ```
 2. 執行主程式

    執行 `var v1 = 10` 以及 `var inner = test()` 。
    ```
    globalEC {
        VO: {
            v1: 10,
            inner: undefined,
            test: func
        },
        scopeChain: globalEC.VO
    }

    test.[[Scope]] = globalEC.scopeChain
    ```

 3. 進入 test EC

    進入 test EC，並初始化 AO and scope chain。
    ```
    testEC {
        AO: {
            arguments,
            vTest: undefined,
            inner: func
        },
        scopeChain: [testEC.AO, globalEC.VO]
    }

    globalEC {
        VO: {
            v1: 10,
            inner: undefined,
            test: func
        },
        scopeChain: globalEC.VO
    }

    test.[[Scope]] = globalEC.scopeChain
    ```

4. 執行 test 程式

    執行 `var vTest = 20` 與 `return inner` 。
    ```
    testEC {
        AO: {
            arguments,
            vTest: 20,
            inner: func
        },
        scopeChain: [testEC.AO, globalEC.VO]
    }

    globalEC {
        VO: {
            v1: 10,
            inner: func,
            test: func
        },
        scopeChain: globalEC.VO
    }

    inner.[[Scope]] = testEC.scopeChain = [testEC.AO, globalEC.VO]
    ```

 5. 執行 `return inner`
  
    理論上 `return inner` 後，function test() 執行完畢後資源會被釋放，但是因為 

    ```
    inner.[[Scope]] = testEC.scopeChain = [testEC.AO, globalEC.VO]
    ```


    inner.[[Scope]] 之中還有需要使用到 testEC.AO 的部分，因此儘管 test 這項 function 執行結束了，但是 `testEC.AO` 仍需要被存在記憶體中。

 6. 進入 inner EC

    進入 inner EC，並初始化 AO and scope chain。

    ```
    innerEC {
        AO: {
            arguments
        },
        scopeChain: [innerEC.AO, testEC.AO, globalEC.VO]
    }

    testEC {
        AO: {
            arguments,
            vTest: 20,
            inner: func
        },
        scopeChain: [testEC.AO, globalEC.VO]
    }

    globalEC {
        VO: {
            v1: 10,
            inner: func,
            test: func
        },
        scopeChain: globalEC.VO
    }

    inner.[[Scope]] = testEC.scopeChain = [testEC.AO, globalEC.VO]
    ```


 7. 執行 inner 

    ```
    var v1 = 10
    function test() {
        var vTest = 20
        function inner() {
            console.log(v1, vTest) // 10, 20
        }
        return inner
    }
    var inner = test()
    inner()
    ```


 8. 執行完畢
---
### 結論
透過上述的拆解流程可以得知，其實當我們在宣告 function 時，程式背後的 compiler 就已經在幫我們建立 EC 以及初始化 EO/AO 的資訊了，並且把 scope 設定到 [[Scope]] 之中，因此當我們在這段程式碼之中：

```
function test () {
  let a = 10
  function inner () {
    console.log(a)
  }
  return inner
}
var inner = test()
inner()
```

使用 `return inner` 時，就能夠把內部的 function inner 回傳，使後續動作可以藉著執行 inner() 進行。而這樣的形式，我們可以說 `inner()` 這項 function 是在一個 `Closure`
之中，因為它也就像是被一項外層的 function 包裹起來。

##### Reference
- [所有的函式都是閉包：談 JS 中的作用域與 Closure](https://github.com/aszx87410/blog/issues/35)