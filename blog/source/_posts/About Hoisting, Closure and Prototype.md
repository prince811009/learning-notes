## Hoisting 變數提升 
 - example_1 ( 變數 )
    ```
    console.log(a)
    var a = 1
    ```
    可以看成 2 步驟拆解： 
   *  first : 將變數拆解
        ```
        console.log(a)
        var a
        a = 1
        ```
   *  second : 再提升變數
    
        因此在程式跑到 ```console.log(a)``` 之前就可以先使用此變數。

        ```
        var a 
        console.log(a)
        a = 1
        ```
---
 - example_2 (function)
   *  正常做法 :
        ```
        function test() {
            console.log(123)
        }

        test()
        ```

   *  因 hoisting 關係，因此以下程式碼也可運行。
        ```
        test()

        function test() {
            console.log(123)
        }
        ```
---
## Closure 閉包
 - example_1
    ```
    function test() {
        var a = 10
        console.log(a)
    }
    // 是否能在 function 外存取變數 a ?

    console.log(a)
    test()
    ```
   * answer : 因作用域的不同，在 function 之外無法存取變數 a，且執行完 function 後，裡面的變數會被同時釋放掉。

 - example_2
    ```
    var count = 0
    function addCount() {
        count++
        return count
    }

    console.log(addCount()) // 1
    console.log(addCount()) // 2
    ```
   *  question : 

        變數可被改變，連帶影響輸出的值，因此應該要以別的方式避免變數被更改。
   ```
    var count = 0
    function addCount() {
        count++
        return count
    }

    console.log(addCount()) // 1
    count = 10
    console.log(addCount()) // 11
    ```
   *  solution : 
    
      再產生一個新的作用域，將變數放置在此，使外部存取不到此變數。( 可簡單想成 -> 在 function 裡 return 一個 function)。
   ```
   function createCounter() {
       var count = 0
       function addCount() {
           count++
           return count
       }
       return addCount
   }

   var counter = createCounter()
   console.log(counter())
   console.log(counter())
   ```
    or
    ```
   function createCounter() {
       var count = 0
       return function () {
           count++
           return count
       }
   }

   var counter = createCounter()
   console.log(counter())
   console.log(counter())
   ```
---
## Prototype
 - example_1 : 
 
   JavaScript 中物件導向的用法
 ```
function Person(name) {
    this.name = name
}

var nick = new Person('nick')
var peter = new Person('peter')
console.log(nick.name, peter.name)
 ```

 - example_2 :
```
function Person(name) {
    this.name = name
    this.getName = function () {
        return this.name
    }
}

var nick = new Person('nick')
var peter = new Person('peter')
console.log(nick.getName())
console.log(nick.getName === peter.getName) // false
// 記憶體指向不同地方
```

 - example_3 :
```
function Person(name) {
    this.name = name
}

Person.prototype.getName = function () {
    return this.name
}

var nick = new Person('nick')
var peter = new Person('peter')
console.log(nick.getName === peter.getName) // true
```
---
   








