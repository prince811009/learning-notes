# JavaScript_prototype chain #1
#### Reference: [該來理解 JavaScript 的原型鍊了](https://github.com/aszx87410/blog/issues/18)


## 先從 JavaScript 中的 class 開始
### JavaScript 中的繼承機制
有趣的是這其中沒有 "子類別" 與 "父類別" 的概念，也沒有區分 ```class``` 與 ```instance``` ，皆是由 ```prototype chain``` 的模式下實現繼承機制。

### JavaScript 設計原理
在早期 ( 西元 1994 年 ) 那時版本的瀏覽器只能單純瀏覽，並沒有辦法與使用者互動，因此當時的 Netscape 希望可用一種網頁腳本語言，讓使用者可以和瀏覽器互動。

Brendan Eich 正是當時研發這項語言的工程師，而那時候的時空背景 object-oriented programming 正流行，Brendan Eich 或許也受到一定程度的影響，因此在 JavaScript 中的數據類型都是 Object 的型態。

### JavaScript 需不需要設計繼承機制 ?
如果是一項簡易的語言，到是不需要考慮到繼承機制問題，只不過 JavaScript 中的數據型態都是以 Object 呈現，那就必須有一項機制將這些物件互相聯繫，因此 JavaScript 仍然設計了繼承機制。

但是 Brendan Eich 沒有打算引入 ```class``` 的概念，但是他把 ```new``` 的命令引入了 JavaScript 中，從原形中生成一個 instance 。
 - C++ 
```
ClassName *object = new ClassName(param);
```

 - Java
 ```
 FOO foo = new Foo();
 ```

而上述提到的 C++, Java 在使用 ```new``` 時，會使用 ```class``` 的 ```constructor``` ，不過因為 JavaScript 沒有引入 ```class``` 的概念，因此使用 ```new``` 時，後面接的不是 ```class``` 而是直接接 ```constructor```。

 - example
 現在有一個叫做 Dog 的 constructor，來表示狗的原形：
 ```
// constructor 
function Dog(name) {
    this.name = name;
}
 ```
我們在使用 ```new``` 生成一個狗的 instance：
```
var dogA = new Dog('Doggy');
console.log(dogA.name); // Doggy
```


#### Reference [JavaScript 中的构造函数](https://juejin.im/entry/584a1c98ac502e006c5d63b8)

### constructor 基本結構
而剛剛提及的 constructor 又是指什麼事情呢 ? 

使用物件導向的第一步，就是生成 instance，而在 JavaScript 中是以 constructor 與 prototype 來進行。生成 instance 時需要一個模板當作我們的藍圖，表示某一類實物的共同特徵，接著再根據這個模板生成。

而 JavaScript 中就是使用 constructor 作為一個模板，每個模板可以生成許多 instance ，每個 instance 都有相同的結構。

 - constructor基本結構
```
var Dog = function() {
    this.height = 100;
}
// or another way

function Dog() {
    this.height = 100;
}
```

 - 特點：與其他函數的區別
   *  constructor 函數名的第一個字母通常為大寫
   *  函數內使用 ```this``` ，代表所要生成的對象 instance
   *  生成 instance 時，必須使用 ```new``` 指令來使用 ```constructor```

### JavaScript 中 new 指令的缺點
使用 ```constructor``` 生成 instance 的缺點為無法共享屬性與 methods

- 例如先在 Dog 的 ```constructor``` 中設置一個 instance 的共同屬性 "species"。

```
function Dog(name) {
    this.name = name
    this.species = '犬科';
}
```
 - 再生成兩個 instance
 ```
 var dogA = new Dog('Doggy');
 var dogB = new Dog('Lucky');
 ```
 - 而這兩的 instance 的 species 的屬性是各自獨立的，就算修改其中之一也不會互相影響。
 ```
 dogA.species = '貓科';
 console.log(dogB.species); // 犬科
 ```

 因此每一個 instance 都擁有屬於自己的屬性和 methods，但這就無法做到屬性 / 數據共享，使不必要的資源浪費；這時就要分成兩個做法，因為有些屬性例如 age, name 很明顯是每個 instance 會不一樣的，但是又會有某一些屬性或是 methods 會是每個 instance 彼此之間可以共享的。

 ### prototype 屬性的引入
考慮到這部份的確點，Brendan Eich 又決定為 ```constructor``` 設置  ```prototype``` 的屬性。所有 instance 需要共享這些屬性和 methods 的都放在這裡，不需要共享的則放在 constructor 裡。

當 instance一旦被創造，將自動引用 ```prototype``` 對象的屬性和 methods，也可以把 instance 的屬性和方法，分成本地端和引用端兩種型態。

 - 使用 ```prototype``` 屬性改寫：
```
function Dog(name) {
    this.name = name;
}

Dog.prototype = {
    species: '犬科'
}

var dogA = new Dog('Doggy');
var dogB = new Dog('Lucky');

console.log(dogA.species); // 犬科
console.log(dogB.species); // 犬科
```
目前 species 的屬性是放在  ```prototype``` 裡面，可以讓兩個 instance 共享，因此只要修改  ```prototype``` ，就會同時影響兩個 instance 。

 - 修改  ```prototype```
 ```
 Dog.prototype.species = '貓科';
console.log(dogA.species); // 貓科
console.log(dogB.species); // 貓科
```

### 結論
你有一個 ```Dog``` 的函數，就可以把 ```Dog``` 當作 ```constructor``` ，再利用
```
var obj = new Dog()
```
來 new 一個 ```Dog``` 的 instance，並且可以在 Dog.prototype 上寫下想要讓 instance 共同享有的屬性或 methods 。
