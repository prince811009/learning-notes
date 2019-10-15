---
title: Scope 作用域 (ES6 以前)
date: 2019-09-07 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
---
### 變數的生存範圍

- Scope 的最基本單位 -  function

    在 JavaScript 中，ES6 之前 Scope 的基本單位為 function，在 function 才能產生一個新的作用域。

    ```
    function test() {
        var a = 10
        console.log(a)
    }

    test() // 10
    console.log(a) // ReferenceError: a is not defined
    ```

    因此這個例子中，function test() 會產生一個 test scope，而 `a` 就會宣告在這個 test 的 scope 裡面，所以 `a` 只有在此 function 裡面才能被看到，在 function 之外就無法看到此變數，出現錯誤 `ReferenceError: a is not defined` 。

<!-- more -->

- global variable

    ```
    var a = 20 // global variable
    function test() {
        console.log(a)
    }

    test() // 20
    console.log(a) // 20
    ```

- 優先順序

    程式會優先尋找 function 中的變數，若無再往上至 global variable 尋找。

    ```
    var a = 20 // global variable
    function test() {
        var a = 10
        console.log(a)
    }

    test() // 10
    console.log(a) // 20
    ```

    若我們將變數範圍印出，可更清楚 scope 範圍
    ```
    var a = 'global'
    function test() {
        var a = 'test scope a'
        var b = 'test scope b'
        console.log(a, b) // test scope a, test scope b 
        function inner() {
            var b = 'inner scope b'
            console.log(a, b) // test scope a, inner scope b
        }
        inner() 
    }

    test() 
    console.log(a) // global
    ```

### scope chain 作用域鍊
由上可得結論，作用域的優先尋找順序為 `inner scope -> test scope -> global scope` 。