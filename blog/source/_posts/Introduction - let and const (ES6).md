---
title: let and const (ES6)
date: 2019-09-21 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/Introduction%20-%20let%20and%20const%20(ES6).md
---
### const

全名為 constant ( 常數 )，相對於 var ( 變數 )，需要一開始即指定一初始值給 const，且不能在後續的步驟修改 const 的值。

<!-- more -->

 - 需先指定一初始值，否則會出現錯誤 `Missing initializer in const declaration`

    ```
    const b
    b = 20 // Error: `Missing initializer in const declaration`
    ```

 - 不能在後續修改 const 的值，否則出現錯誤 `Assignment to constant variable` 。

    ```
    const b = 10
    b = 20 // Error: `Assignment to constant variable`
    ```

  - let 以及 var 則無此限制


### 應用
 - example 01 :

    ```
    const obj = {
      number: 1
    }

    obj.number = 2
    ```

    此時當執行 `obj.number` 時，程式會去修改其中 `obj` 所儲存的記憶體位置中的  `number` 的值，而不會去修改到記憶體位置，因此不會出錯，可想像成

    ```
    0x01: {
      number: 2
    } // 其中的 number 的值修改成 2

    obj:0x01

    const obj = {
      number: 1
    }

    obj.number = 2
    ```

 - example 02 :

    ```
    const obj = {
      number: 1
    }

    obj = {
      number: 2
    }
    ```

   這時已產生一個新的記憶體位置來放 `number: 2` 的值，之後再將這新的記憶體位置只給 `obj` 但此時已修改到 `const obj` 的值了，因此會發生錯誤。
    
    ```
    0x01: {
      number: 2
    }

    0x02: {
      number: 2
    }

    obj:0x02 // obj 的值 ( 也就是儲存的記憶體位置 ) 被修改了

    const obj = {
      number: 1
    }

    obj = {
      number: 2
    } // Error: `Assignment to constant variable`
    ```

