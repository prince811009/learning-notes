---
title: let and const (ES6) 的生存範圍
date: 2019-09-08 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/Scope%20-%20let%20and%20const%20(ES6).md
---
### 前言：

在 ES6 之前，scope 作用域的最基本單位為 function，只要有 function 就會產生新的作用域；但若在 ES6 之後，最基本單位為 `block` 。

<!-- more -->

### scope

 - var

    作用域範圍為 function 之內都可使用

    ```
    function() {
        var a = 60
        if (a === 60) {
            var b = 10 
        }
        console.log(b) // 10
    }

    test() 
    ```

 - let or const
    *  example 01 : 

        ```
        function() {
            var a = 60
            if (a === 60) {
                let b = 10 
            }
            console.log(b) // b is not defined
        }

        test() 
        ```

        因為在 ES6 之後，使用 `let` or `const` ，其作用域會存在於 block 中，因此在這個狀況下跳脫出 `if` 的範圍外後，就無法存取到 `b` 。

    *  example 02 :  

        - 使用 var 宣告的情況下：

        ```
        function test() {
            for (var i=0; i<10; i++) {
                console.log('i:', i)
            }
            console.log('final value:', i)
        }

        test()
        ```

        * answer: 
            ```
            i: 1
            i: 2
            i: 3
            i: 4
            i: 5
            i: 6
            i: 7
            i: 8
            i: 9
            final value: 10 // 可存取到 function 中的所有變數
            ```

        - 使用 let or const 宣告的情況下：
        ```
        function test() {
            for (let i=0; i<10; i++) {
                console.log('i:', i)
            }
            console.log('final value:', i)
        }

        test()
        ```

        * answer: 
            ```
            i: 1
            i: 2
            i: 3
            i: 4
            i: 5
            i: 6
            i: 7
            i: 8
            i: 9
            i is not defined
            ```
