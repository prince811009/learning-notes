---
title: Webpack 超入門安裝
date: 2019-10-05 10:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/Webpack.md
---

 - Global Installation
    ```
    npm install webpack --save-dev
    ```
 - 建立專案資料夾
     *  ```mkdir <folder name>```
     *  ```cd <folder name>```
 - 建立 npm 專案
     *  ```npm init```
        1. ```package name``` : 專案名稱
        2. ```version``` : 版本號
        3. ```description``` : 專案敘述
        4. ```entry point``` : 專案執行進入點 ( 預設值為 index.js)
        5. ```test ccommand``` : 專案測試指令
        6. ```git repository``` : 專案原始碼的版本控管位置
        7. ```keywords``` : 專案關鍵字
        8. ```author``` : 專案作者
        9. ```license``` : 專案版權


<!-- more -->

 - 建置文件
     *  src/index.js
        ```
        import bar from './bar';

        bar();
        ```
     *  src/bar.js
        ```
        export default function bar() { 
          //
        }
        ```

 - 設定 webpack.config.js 檔
    ```
    const path = require('path'); // 引用 path 模組

    module.exports = {
        entry: './src/index.js', // webpack 打包對象
        output: {
            path: path.resolve(__dirname, 'dist'),
            filename: 'bundle.js'
        }
    };
    ```
 - 在 html / or 文件中引入
    ```
    <!doctype html>
    <html>
        <head>
            ...
        </head>
        <body>
            ...
            <script src="dist/bundle.js"></script>
        </body>
    </html>
    ```
 - 執行
    ```
    npx webpack --config webpack.config.js
    ```


##### Reference
 - [webpack](https://webpack.js.org/)
 - [Installation](https://webpack.js.org/guides/installation/)
 - [使用 Webpack 綁定應用程式](https://docs.aws.amazon.com/zh_tw/sdk-for-javascript/v2/developer-guide/webpack.html)
 - [webpack 模組綁定程式](https://docs.aws.amazon.com/zh_tw/sdk-for-javascript/v2/developer-guide/webpack.html)