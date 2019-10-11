---
title: Hoisting - 從 ECMAScript 下手
date: 2019-09-23 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/Hoisting%20-%20%E5%BE%9E%20ECMAScript%20%E4%B8%8B%E6%89%8B.md
---
讓我們從個小測驗著手吧

<!-- more -->


### quiz
    ```
    var a = 1
    function test() {
        console.log('1.', a)
        var a = 7
        console.log('2.', a)
        a++
        var a
        inner()
        console.log('4.', a)
        function inner(){
            console.log('3.', a)
            a = 30
            b = 200
        }
    }
    test()
    console.log('5.', a)
    a = 70
    console.log('6.', a)
    console.log('7.', b)
    ```

 - answer : 
    
    以 hoisting 試想

    ```
    var a = 1
    function test() {
        var a
        console.log('1.', a) // '1. undefined'
        a = 7
        console.log('2.', a) // '2. 7'
        a++
        var a

        function inner() {
            console.log('3.', a) // '3. 8'
            a = 30
            b = 200
        }

        inner()
        console.log('4.', a) // '4. 30'
    }

    test()
    console.log('5.', a) // '5. 1'
    a = 70
    console.log('6.', a) // '6. 70'
    console.log('7.', b) // '7. 200'
    ```
---
### ECMAScript
 - Execution Contexts
    
    When control is transferred to ECMAScript executable code, control is entering an _execution context_. Active execution contexts logically form a stack. The top execution context on this logical stack is the running execution context.

 - Variable Instantiation

    Every execution context has associated with it a variable object. Variables and functions declared in the source text are added as properties of the variable object. For function code, parameters are added as properties of the variable object.

    ```
    VO: {
        a:1
    }

    function test() {
        var a = 1
    }
    ```

    On entering an execution context, the properties are bound to the variable object in the following order:
    *  For function code: for each formal parameter, as defined in the FormalParameterList, create a property of the variable object whose name is the Identifier and whose attributes are determined by the type of code. The values of the parameters are supplied by the caller as arguments to [[Call]].  

        ```
        VO: {
            a: 123,
            b: undefined
        }

        function test(a, b) {
        
        }

        test(123) // 會使參數初始化
        ```

    *  For each FunctionDeclaration in the code, in source text order, create a property of the variable object.

        If the variable object already has a property with this name, replace its value and attributes.

        ```
        VO: {
            a: 123,
            b: function
        }

        function test(a, b) {
            function b() {

            } // 若有同名的，則會被後面的所覆蓋
        }

        test(123)
        ```

    *  For each VariableDeclaration or VariableDeclarationNoIn in the code, create a property of the variable object whose value is undefined.

        If there is already a property of the variable object with the name of a declared variable, the value of the property and its attributes are not changed. 
        
        ```
        VO: {
            a: 123,
            b: function,
            c: undefined
        }

        function test(a, b) {
            function b() {
            }
            var c = 30
        }

        test(123)
        ```
---
了解原理後，回到上述的 quiz
### quiz
```
var a = 1
function test(){
  console.log('1.', a)
  var a = 7
  console.log('2.', a)
  a++
  var a
  inner()
  console.log('4.', a)
  function inner(){
    console.log('3.', a)
    a = 30
    b = 200
  }
}
test()
console.log('5.', a)
a = 70
console.log('6.', a)
console.log('7.', b)
```
 - answer : 
   
   體驗在 JS 引擎中此段程式碼如何被執行
 1. 產生 global Execution Context，並初始化 global VO

    ```
    global EC
    global VO {
    }
    ```
  2. 首先尋找參數，但因為此段並不是 function 所以沒有參數。
  3. 接著尋找是否有 function 的宣告 -> 找到 `test()` 的宣告。
        ```
        global EC
        global VO {
            test: func
        }
        ```
 4. 尋找變數宣告
    ```
    global EC
    global VO {
        test: func,
        a: undefined
    }
    ```
 5. 開始執行程式
    
    *  Line 1 : `var a = 1;`
        ```
        global EC
        global VO {
            test: func,
            a: 1
        }
        ```
    *  Line 2~15 : function 宣告跳過
    *  Line 16 : 當呼叫一個新的 function `test()` ，就進入一個新的 `execution context` 。
---
 6. 產生新的 test Execution Context ，並初始化 test VO :

    ```
    test EC
    test VO {

    }
    
    global EC
    global VO {
        test: func,
        a: 1
    }
    ```

 7. 首先尋找參數 -> 無
 8. 接著尋找是否有 function 的宣告 -> 找到 `inner()` 的宣告。

    ```
    test EC
    test VO {
        inner: func
    }
    
    global EC
    global VO {
        test: func,
        a: 1
    }
    ```

 9. 尋找變數宣告

    ```
    test EC
    test VO {
        inner: func,
        a: undefined
    }
    
    global EC
    global VO {
        test: func,
        a: 1
    }
    ```

 10. 開始執行程式

     *  Line 3 : `console.log('1.', a) // undefined`
     *  Line 4 : `var a = 7`

         ```
        test EC
        test VO {
            inner: func,
            a: 7
        }
        
        global EC
        global VO {
            test: func,
            a: 1
        }
        ```

     *  Line 5 : `console.log('2.', a) // 7`
     *  Line 6 : `a++`

        ```
        test EC
        test VO {
            inner: func,
            a: 8
        }
        
        global EC
        global VO {
            test: func,
            a: 1
        }
        ```

     *  Line 8 : `inner()`
---
 11. 產生新的 inner Execution Context ，並初始化 inner VO :

        ```
        inner EC
        inner VO {

        }

        test EC
        test VO {
            inner: func,
            a: 8
        }
        
        global EC
        global VO {
            test: func,
            a: 1
        }
        ```

 12. function inner() 中沒有傳入任何參數，裡面也沒有 function 以及變數。

        ```
        inner EC
        inner VO {

        }

        test EC
        test VO {
            inner: func,
            a: 8
        }
        
        global EC
        global VO {
            test: func,
            a: 1
        }
        ```

 13. 開始執行程式
    *  Line 11 : `console.log('3.', a) // 8`
        
        ```
        inner EC
        inner VO {

        }

        test EC
        test VO {
            inner: func,
            a: 8
        }
        
        global EC
        global VO {
            test: func,
            a: 1
        }
        ```

        因為 inner VO 裡面是空的，因此往上找至 test VO -> a: 8 。
     *  Line 12 : `a = 30`
        
        ```
        inner EC
        inner VO {

        }

        test EC
        test VO {
            inner: func,
            a: 30
        }
        
        global EC
        global VO {
            test: func,
            a: 1
        }
        ```

     *  Line 13 : `b = 200`

        因為 inner VO 裡面沒有 b 的資訊，因此往上找至 test VO ，發現裡面也沒有 b 的資訊，繼續往上至 global VO 找，仍沒有 b 的資訊，最後在 global VO 建立 b 的資訊 ( 讓 b 變成全域變數 )。

        ```
        inner EC
        inner VO {

        }

        test EC
        test VO {
            inner: func,
            a: 30
        }
        
        global EC
        global VO {
            test: func,
            a: 1,
            b: 200
        }
        ```

 14. 執行完 inner()，將 inner EC pop 出去。

        ```
        test EC
        test VO {
            inner: func,
            a: 30
        }
        
        global EC
        global VO {
            test: func,
            a: 1,
            b: 200
        }
        ```

 15. 回到 function test() {} 內部
     *  Line 9 : `console.log('4.', a) // 30`
 16. 執行完 test()，將 test EC pop 出去。

        ```  
        global EC
        global VO {
            test: func,
            a: 1,
            b: 200
        }
        ```

 17. 回到 global EC
     *  Line 17 : `console.log('5.', a) // 1`
     *  Line 18 : `a = 70`

        ```  
        global EC
        global VO {
            test: func,
            a: 70,
            b: 200
        }
        ```

     *  Line 19 : `console.log('6.', a) // 70`
     
     *  Line 20 : `console.log('7.', a) // 200`
 18. 主程式執行完畢

 ##### Reference 
 - [ECMA-262 - Ecma International
](https://www.ecma-international.org/publications/files/ECMA-ST-ARCH/ECMA-262,%203rd%20edition,%20December%201999.pdf)
 - [我知道你懂 hoisting，可是你了解到多深？](https://github.com/aszx87410/blog/issues/34)
