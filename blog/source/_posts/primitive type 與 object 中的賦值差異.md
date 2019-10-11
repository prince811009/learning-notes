---
title: primitive type 與 object 中的賦值差異
date: 2019-09-13 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/primitive%20type%20%E8%88%87%20object%20%E4%B8%AD%E7%9A%84%E8%B3%A6%E5%80%BC%E5%B7%AE%E7%95%B0.md
---

### 賦值差異
 - 在 primitive type 中

  變數 a 並不會跟著一起改變

  ```
  var a = 10
  var b = a
  console.log(a, b) // 10, 10
  b = 200
  console.log(a, b) // 10, 200
  ```  

<!-- more -->

 - 在 object 中
  
  obj 也連同 obj2 一起改變

  ```
  var obj = {
    number: 10
  }
  var obj2 = obj
  console.log(obj, obj2) // {number: 10} {number: 10}
  obj2.number = 20
  console.log(obj, obj2) // {number: 20} {number: 20}
  ```



### 原理
 - 在 primitive type 中

  ```
  var a = 10
  var b = a
  console.log(a, b) // 10, 10
  b = 200
  console.log(a, b) // 10, 200
  ```  

  可以由儲存的記憶體位置來試想，當我們宣告  `var a = 10` 時，

  ```
  a: 10 // 記憶體 a 位置，儲存的值 = 10
  ```

  當我們宣告 `var b = a` 時，會把 a 的值複製給 b ，所以 b 的記憶體位置也會存 10 。

  ```
  a: 10
  b: 10
  ```

  當我們下指令 `b = 20` 時，此時就將 b 位置的值修改成 20。

  ```
  a: 10
  b: 20
  ```

- 在 object 中

  ```
  var obj = {
    number: 10
  }

  var obj2 = obj
  console.log(obj, obj2) // {number: 10} {number: 10}
  obj2.number = 20
  console.log(obj, obj2) // {number: 20} {number: 20}
  ```

  當我們宣告 obj 時，會將 obj 真正的值儲存在某記憶體位置

  ```
  var obj = {
    number: 10
  }
  ```

  可試想儲存在記憶體 0x01 的位置

  ```
  0x01 {
    number: 10
  }
  ```

  此時回頭看 obj 儲存的值其實是這個記憶體位置

  ```
  obj: 0x01
  ```

  因此當我們宣告 `var obj2 = obj` 時，會將 obj 的值，也就是記憶體位置複製給 obj2，所以現在 `obj` 與 `obj2` 會指向同一個記憶體位置。
  
  ```
  0x01 {
    number: 10
  }

  obj: 0x01
  obj2: 0x01
  ```

  最後當我們執行 `obj2.number = 20` 來改變 obj2 的值的時候，會去找 obj2 的記憶體位置的 number 修改成 20，所以 obj 和 obj2 log 出來的值就會變成相同。
  
  ```
  0x01: {
    number: 20
  }

  obj: 0x01
  obj2: 0x01
  ``` 

  若再將例子變換一些，
  
  ```
  var obj = {
    number: 10
  }

  var obj2 = obj
  console.log(obj, obj2) // {number: 10} {number: 10}
  obj2 = {
    number: 30
  }
  console.log(obj, obj2) // {number: 10} {number: 30}
  ```

  在 `var obj = { number: 10 }` 時，會先建立新的記憶體位置 0x01，再把記憶體位置給予 obj，而 obj2 同上，也會建立另一個新的記憶體位置 0x02，再將記憶體位置給予 obj2。

  ```
  0x01: {
    number: 10
  }

  0x02: {
    number: 30
  }

  obj: 0x01
  obj2: 0x02
  ``` 