---
title: Is JavaScript a call-by-value or call-by-reference language?
date: 2019-10-14 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/call%20by%20value%20or%20reference.md
---

### function 參數傳遞方式
先來說明第一種方式：
1. call by value / pass by value
    直接舉個例子來看
    ```
    function swap(a, b) {
        var temp = a;
        a = b;
        b = temp;
    }

    var x = 10;
    var y = 20;
    swap(x, y);
    console.log(x, y); // 10, 20
    ```
    - 這是個很有趣的現象，我們上述例子的用意在於希望把 x, y 的值交換，先把 a 指向 temp ( 我們預留的空位 )，把 b 指向給 a ，最後再把 temp 指向給 b。但是其實上述的例子並沒有真的把 `x` 和 `y` 的值進行交換，因為我們拿出來交換的東西並不是真正的 `x` 和 `y` ，而是 `x` 和 `y` 的值的副本。

    - 可以把我們宣告的 `a` 和 `b` 想像成兩個新的變數，只是剛好存的值與  `x` 和 `y` 一樣，所以就算改變了 `a` 也不會改變 `x` ，因為他們本身就是不同的變數 ( 記憶體位置打從一開始就不同 ) 。

<!-- more -->

2. call by reference
   可以說是和上述第一種方式相反，傳進去的參數則是真的  `x` 和 `y`，在 function 裡面宣告的  `a` 和 `b` 只是 alias ，如果我們改變了 `a` 就真的會因此改變 `x` 。
---
### 適用情境
- 數字
    
    在 JavaScript 中一般情境，例如上面 example 中的 Primitive type ( 原始型別 )，只會使用 call by value ，因為我們無法透過 function 中的引數去改變 function 外的變數。

- 物件
    *  看起來像是 call by sharing

        ```
        function add(obj) {
            obj.number++
        }

        var o = {
            number: 10
        }
        add(o)
        console.log(o.number) // 11
        ```
---    
### 事實其實是...
在看到 Huli 這篇文章所引用的句子時著實也嚇了一跳，「JavaScript 只有 call by value」。

在宣告一個 object 時，在底層實作上，Object 被存取的地方是一個記憶體的位置，概念很像 C 語言的指標，儲存的值是「記憶體位置」。

例如，當我們宣告一個變數時，

```
var o = {
    number: 10
}
```

如果把他具像化的表示的話可能會像這樣：


| 變數名稱 | 記憶體位置 | 儲存的值 |
| ------- |:---------:| --------:|
|         | 0x01   | {number: 10}|
| o       | 0x02      | 0x01    |


- 因此如果從上層來看，`o` 的值為 `{number: 10}` ，不過若從底層時做的角度來看，`o` 的值為記憶體位置的 `0x01`。

- 也就是說在 function 裡面的變數是透過「記憶體位置」去操作這些東西，因此 `o` 和 `obj` 兩個變數的指標指向了同一個記憶體位置，而底層實作原理就是把 `o` 的記憶體位置傳給 `obj` ，所以才能指著同一個位置。

- 因此我們在前面提到 "適用情境" 中的物件時，這裡提到的 `call by sharing` ( 傳記憶體位置進去 )，可說是 `call by value` 的一種，因為原理同樣是傳一個值的備份給它，指是這邊指的 "值" 是記憶體的位置。

### 結論
#### 定義
- `call by value` 會把傳進去的值先複製一份 (可以是數值、記憶體位置 )，`call by reference` 在底層實作上也有類似的行為。

- 在 `call by reference` 中， `x` 的記憶體位置和 `a` 相同， `y` 的記憶體位置和 `b` 相同，因此你可以說他們兩兩是相同的。

- 不過在 `call by value` 中，就算傳給對方的是指標，也只是代表「指標內所儲存的值」，有就是把記憶體的位置指給對方，或說是指向相同的記憶體位置，但是指標本身還是有不同的記憶體位置 ( 繞口 ~)。

- 若是想像成，在 `call by value` 時，等於是新增了一個變數 `a` ，讓 `a` 儲存的值和傳進來的參數相同，這樣的想法會比較直覺；而 `call by reference` 則是把 `a` 當作 `x` 的 alias ( 別名 )，但是其實兩者是相同的變數。

#### 從行為上區分
從「行為」本身上區分兩者是比較容易的方法，不同的種類達成的行為不同。

- 條件 : 在 function 中對引數重新賦值，是否會影響外面的變數
   *  會 -> `call by reference`
   *  不會 -> `call by value`

---
##### Reference
- [深入探討 JavaScript 中的參數傳遞：call by value 還是 reference?](https://github.com/aszx87410/blog/issues/30)
- [Is JavaScript a pass-by-reference or pass-by-value language?](https://stackoverflow.com/questions/518000/is-javascript-a-pass-by-reference-or-pass-by-value-language)
- [Evaluation strategy](https://en.wikipedia.org/wiki/Evaluation_strategy#Call_by_sharing)
- [Parameter Passing](https://www.python-course.eu/passing_arguments.php)
- [[筆記] 談談JavaScript中by reference和by value的重要觀念](https://pjchender.blogspot.com/2016/03/javascriptby-referenceby-value.html)
- [重新認識 JavaScript: Day 05 JavaScript 是「傳值」或「傳址」?](https://ithelp.ithome.com.tw/articles/10191057)