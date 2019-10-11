---
title: JavaScript 變數的資料型態
date: 2019-09-20 18:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/JavaScript%20%E8%AE%8A%E6%95%B8%E7%9A%84%E8%B3%87%E6%96%99%E5%9E%8B%E6%85%8B.md
---
 - primitive type ( 原始型態 )
   *  null
   *  undefined
   *  string
   *  number
   *  boolean
   *  symbol (ES6) 
 - others
   *  object(array, function, date...)

<!-- more -->

 - primitive type 與 object 資料型態的差別

    其中最大差別在於 primitive type 為 Immutable

    ```
    var str = 'hello'
    var newStr = str.toUpperCase()
    console.log(str, newStr) // hello HELLO

    var arr = [1]
    arr.push(2)
    console.log(arr) // [1, 2]
    ```


### typeof 
 - 可使用 typeof 了解目前的資料型態

   ```
   console.log(typeof 42);
   // expected output: "number"

   console.log(typeof 'blubber');
   // expected output: "string"

   console.log(typeof true);
   // expected output: "boolean"

   console.log(typeof declaredButUndefinedVariable);
   // expected output: "undefined";
   ```
   
 - answer
    ```
    "number"
    "string"
    "boolean"
    "undefined"
    ```

### Additional information
 - null

   In the first implementation of JavaScript, JavaScript values were represented as a type tag and a value. The type tag for objects was 0. null was represented as the NULL pointer (0x00 in most platforms). Consequently, null had 0 as type tag, hence the "object" typeof return value.

   ```
   // This stands since the beginning of JavaScript
   typeof null === 'object';
   ```
### Description
The following table summarizes the possible return values of typeof

| Type | Result |
| -- | -- |
| Undefined | "undefined" |
| Null | "object" (see below) |
| Boolean | "boolean" |
| Number | "number" |
| BigInt | "bigint" |
| String | "string" |
| Symbol (new in ECMAScript 2015) | "symbol" |
| Host object (provided by the JS environment) | Implementation-dependent |
| Function object (implements [[Call]] in ECMA-262 terms) | "function" |
| Any other object | "object" |
---
但因為使用 `typeof ` 在某些情況下可能不是很準確，例如無法檢測出是否為 array，因此可使用另外兩種方式

### Array.isArray()
The Array.isArray() method determines whether the passed value is an Array.

```
Array.isArray([1, 2, 3]);  // true
Array.isArray({foo: 123}); // false
Array.isArray('foobar');   // false
Array.isArray(undefined);  // false
```

### prototype
```
console.log(Object.prototype.toString.call('123'))
console.log(Object.prototype.toString.call(456))
console.log(Object.prototype.toString.call([]))
console.log(Object.prototype.toString.call(new Date()))
console.log(Object.prototype.toString.call(null))

```
 - answer
    ```
    [object String]
    [object Number]
    [object Array]
    [object Date]
    [object Null]
    ```
##### Reference
 - [typeof
](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)
 - [Array.isArray()
](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray)