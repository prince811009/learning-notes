## window.setTimeout()

在先前的 `Event Loop + Scope` 實作中，發現一項有趣的返回值

```
for(var i=0; i<5; i++) {
  console.log('i: ' + i)
  setTimeout(() => {
    console.log(i)
  }, i * 1000)
}
```
- 此時使用 node.js 中會得到的答案為： 
    ```
    i: 0
    i: 1
    i: 2
    i: 3
    i: 4
    5 (第 0 秒)
    5 (第 1 秒) 
    5 (第 2 秒) 
    5 (第 3 秒) 
    5 (第 4 秒) 
    ```

- 但如果使用瀏覽器中的 devTools 則會出現此項現象：

    ```
    i: 0
    i: 1
    i: 2
    i: 3
    i: 4
    此行會多出一個數值
    5 (第 0 秒)
    5 (第 1 秒) 
    5 (第 2 秒) 
    5 (第 3 秒) 
    5 (第 4 秒) 
    ```
    可能會依據所使用的不同瀏覽器 (Chorme, Mozilla Firefox) 而回傳一個不同的數值，甚至是使用一般分頁或是無痕模式分頁也會有所不同。
---

### setTimeout() 
The setTimeout() sets a timer which executes a function or specified piece of code once the timer expires.

根據 MDN 定義 setTimeout() 的作用，會在延遲某段時間後，執行之中的程式碼

 - Return value : ```timeoutID```

    返回值 ```timeoutID``` 會是一個正整數，來表示 timer 的編號。之後這項值可以傳遞給 ```clearTimeout()``` 來取消 timer 使用。

    因此當我們使用這段程式碼在不同瀏覽器或是不同狀態下的分頁的 devTools 進行測試時：
    ```
    setTimeout(() => {}, 0)
    ```
    可以得到不同的數值，原因是這和瀏覽器背後處理過幾次 ```setTimeout()``` 有關。

### Example
We can set a timeout after few seconds and store the timeoutID for use by clearTimeout().

```
var timeoutID

function delayedAlert() {
  timeoutID = window.setTimeout(window.alert, 2 * 1000, 'yo!')
}

function clearAlert() {
  window.clearTimeout(timeoutID)
}
```
 

### Reference
 - [WindowOrWorkerGlobalScope.setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)
 - [WindowOrWorkerGlobalScope.clearTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/clearTimeout)
