---
title: why do we need Gulp ? 
date: 2019-09-28 22:43:10
categories:
- Documentation
tags:
- JavaScript
toc: true
navbar_links:
  Edit on GitHub:
    icon: fas fa-edit
    url: https://github.com/prince811009/prince811009.github.io/blob/source/blog/source/_posts/Gulp%20-%20automate%20and%20enhance%20your%20workflow.md
---
> Automate and enhance your workflow  

目的加強工作流程並自動化。

> Gulp 為一個開源的 Task runner ( 工作執行器 )，建構在 node.js 和 npm 上，自動化整合套件。

<!-- more -->

 - 提供的 API : 
   *  gulp.task : 執行工作
   *  gulp.src : 執行資料來源
   *  gulp.dest : 執行結果位置
   *  gulp.watch : 監視執行過程中資料是否變更

 - 官方 [gulpfile.js](https://gulpjs.com/)

      ```
      // 引用 gulp plugin
      const { src, dest, parallel } = require('gulp'); 
      const pug = require('gulp-pug');
      const less = require('gulp-less');
      const minifyCSS = require('gulp-csso');
      const concat = require('gulp-concat');

      // 建立 gulp task
      function html() {
      return src('client/templates/*.pug')
         .pipe(pug())
         .pipe(dest('build/html'))
      }

      function css() {
      return src('client/templates/*.less')
         .pipe(less())
         .pipe(minifyCSS())
         .pipe(dest('build/css'))
      }

      function js() {
      return src('client/javascript/*.js', { sourcemaps: true })
         .pipe(concat('app.min.js'))
         .pipe(dest('build/js', { sourcemaps: true }))
      }

      exports.js = js;
      exports.css = css;
      exports.html = html;
      exports.default = parallel(html, css, js);          
      ```

### 安裝 Gulp 流程
 - Check for node, npm, and npx

    ```
    node --version
    npm --version
    npx --version
    ```

 - Install the [gulp](https://gulpjs.com/) command line utility

    ```
    npm install gulp-cli -g // 在 global 環境下安裝 gulp
    npm install gulp -D
    npx -p touch nodetouch gulpfile.js // 設定檔放置在 gulpfile.js
    gulp --help
    ```

 - 安裝 gulp 相關套件 ( 以下列舉常見選項 )
     *  [gulp-sass](https://www.npmjs.com/package/gulp-sass)
     *  [gulp-uglify](https://www.npmjs.com/package/gulp-uglify)
     *  [gulp-compass](https://www.npmjs.com/package/gulp-compass)
     *  [gulp-babel](https://www.npmjs.com/package/gulp-babel)
     *  [gulp-less](https://www.npmjs.com/package/gulp-less)
     *  [gulp-rename](https://www.npmjs.com/package/gulp-rename)
     *  [gulp-clean-css](https://www.npmjs.com/package/gulp-clean-css)
     *  [del](https://www.npmjs.com/package/del)

 - Create a project

    ```
    npx mkdirp my-project
    cd my-project
    ```

 - Create a package.json file in your project directory

    ```
    npm init
    ```

 - Create a file named gulpfile.js in your project root with these contents :

    ```
    function defaultTask(cb) {
    // place code for your default task here
    cb();
    }

    exports.default = defaultTask
    ```

    當我們安裝 gulp 相關套件時，`Usage` 的地方都有提供詳細的引入方式。

 - start `Gulp` ~ 
    
    ```
    gulp || gulp <task> <othertask>
    ```

### 實作 Gulp 範例
 - 設定 gulpfile.js 檔

   ```
   const gulp = require('gulp'); // 載入 gulp
   const sass = require('gulp-sass'); // compile your Sass files
   const babel = require('gulp-babel'); // 將 ES6 以上語法轉換成最基本 JS 語法
   const uglify = require('gulp-uglify'); // minify JavaScript
   const rename = require('gulp-rename'); // rename files
   const cleanCSS = require('gulp-clean-css'); // minify CSS
   const del = require('del'); // Delete files and directories using globs

   function clean() {
      return del('./build/*');
   }

   function styles() {
      return gulp.src('./src/css/app.scss') // 輸入路徑
         .pipe(sass())
         .pipe(cleanCSS())
         .pipe(rename({
            basename: 'main',
            suffix: '.min'
      }))
      .pipe(gulp.dest('./build/css')); // 輸出路徑
   }

   function scripts() {
      return gulp.src('./src/js/*.js')
         .pipe(babel())
         .pipe(uglify())
         .pipe(gulp.dest('./build/js'));
   }
   
   function watch() {
      gulp.watch('./css/style.scss', styles);
      gulp.watch('./js/*.js', scripts);
   }

   exports.clean = clean;
   exports.styles = styles;
   exports.scripts = scripts;
   exports.watch = watch;
   exports.default = gulp.series(clean, styles, scripts); // 定義名稱為 default 的 gulp task
   ```

 - `watch`
 
    例如，當我們使用 `gulp-uglify` 用來 minify JavaScript 時，會希望每次 JavaScript 有異動時， `gulp-uglify` 可以監看並自動執行最小化。

      ```
      function watch() {
         gulp.watch('./css/style.scss', styles);
         gulp.watch('./js/*.js', scripts);
      }
      ```
   因此上述程式碼可以協助我們監看 css 以及 js 檔是否有異動來自動執行 `function styles()` and `function scripts()` 。

---
#### Reference
 - [Gulp 官網](https://gulpjs.com/docs/en/getting-started/quick-start)
 - [Gulp 安裝與介紹](https://ithelp.ithome.com.tw/articles/10185420)
 - [gulp](https://www.npmjs.com/package/gulp)
 - [gulp 學習筆記](https://kejyuntw.gitbooks.io/gulp-learning-notes/)
 - [gulp 入門指南](https://987.tw/2014/07/09/gulpru-men-zhi-nan/)
