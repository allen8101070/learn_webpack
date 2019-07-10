# NOTE 1 基礎知識
## 何謂 Webpack?
![](https://i.imgur.com/RdfFo69.png)
隨著 Node.js 的誕生與前端開發的系統化，我們在撰寫 HTML、CSS、JS 的開發環境已不如古早直接 `<script>` 引入檔案這麼單純。

現在的開發者多半會在 Node.js 為主的環境下開發，讓 Node 幫我們編譯或自動化執行一些動作後，前端工程師再將編譯好的檔案上線，而講到自動化和編譯，就會提到 Webpack 的大師兄： **Gulp**

### Webpack 和 Gulp 差異
最初使用 Webpack 和 Gulp 會感覺好像是差不多的自動化工具，總之就是 node 裝了一些套件後，然後 Gulp 或 Webpack 可以幫我們編譯如： `SASS`、`SCSS` `LESS` `PUG` 或什麼 `Sprite` 雪碧圖之類的。

恩..如果只是以這樣的角度來看，那當然不會差太多，但兩者在地位上還是有差異：

#### Gulp
![](https://i.imgur.com/ji6l3Jt.png)
Gulp 是**任務管理工具**，主要是可以自動化處理、編譯我們的程式碼、樣式或轉換檔案格式等功能。

雖然 Gulp 編譯速度很快，但主力還是在於排程靜態檔案的編譯**任務**。隨著 SPA 開發模式的崛起，為了因應 SPA 的開發模式，前端會將檔案拆成一個一個的模組元件，而 Gulp 在處理這些散落的模組化程式碼是相對弱勢的。

當然原因也可以說，在 ES6 普及前，JS 並沒有一個 **統一的** 模組化載入匯出語法，所以相對 Webpack 較早的 Gulp 才會顯得力不從心。


#### Webpack
![](https://i.imgur.com/cVubk0d.png)
Webpack 是**打包工具**，它的崛起和 SPA、ES6 有關，為了因應 SPA 的開發模式，前端會將檔案拆成一個一個的模組元件，利用 ES6 的 import/export 語法動態載入我們的模組程式碼，最後再編譯打包出來。

相較於 Gulp，Webpack 在處理這種所謂模組化(模塊化)的程式碼片段靈活了許多，所以 Angular-cli、Vue-cli、Create-react-app 預設都帶了 Webpack 來幫助開發者最後的打包檔案輸出。

所以才會常聽別人說到：

> Gulp 是任務自動化工具，Webpack 是打包工具

這兩者還是略有差異，並且可以混用的。

## 安裝與建立
雖然我們已經很熟悉 Vue cli 或 CRA 這種已經包了 Webpack 的開發環境
但這邊練習從 0 開始
所以首先還是起一個 node 專案，在終端機下
```
npm init
```
然後照著提示輸入訊息，總之就建個專案，建好後在終端機下指令安裝 `wrbpack` 和 `wrbpack-cli`
```
npm install webpack webpack-cli --save-dev
```
`cli` 和 `--save` 這些前端應該都懂了，不解釋意思

然後在根目錄建立一個 `webpack.config.js` 和 `index.js`

`index.js`內我們可以隨便寫一些 JavaScript，看是要console.log還是宣告變數 if else 計算什麼的，反正那不是重點。

重點是我們建立的 `webpack.config.js` ，他會是整個 Webpack 的設定檔，我們在 `webpack.config.js` 內寫：
```
module.exports = {
    entry: './index.js',
    output: {
        filename: 'index.bundle.js'
    }
};
```
`module.exports` 是 Node.js 語法，這裡不解釋。

Webpack 概念很簡單，就是 input 和 output 輸入輸出
* **entry**：輸入的檔案與路徑
* **filename**: 輸出後的檔案名稱
filename 這裡寫`'index.bundle.js'`，其實名稱要取什麼可以自取，不一定要取`'index.bundle.js'`，但一般來說會建議和編譯前檔案相同名稱並加上 `.bundle` 或 `-bundle` 來表明是壓縮過後的檔案。

這裡沒有加上輸出後的路徑位置，預設就會自動打包輸出在 `./dist` 這裡
若沒有 `dist` 打包時 webpack 會幫我們自動創建一個

接著在 package.json 的 `script` 的部分加入 `"start": "webpack"`
```
"scripts": {
    "start": "webpack",
```

在終端機下
```
npm run start
```
這裡的 start 就是對應 `script` 內的 `start` 這邊相信大家都很熟了
這時就會執行 webpack 打包程式碼，動態創建 dist資料夾在根目錄，並且裡面有我們打包好的程式碼。

## 檔案路徑

### entry 輸入
延續上面的設定
```
module.exports = {
    entry: './index.js',
    output: {
        filename: 'index.bundle.js'
    }
};
```
`entry` 這裡的路徑若寫成 **絕對路徑**，當然是可以的
只是如果用**絕對路徑**寫死檔案輸入/輸出位置，那遷移資料夾時會非常麻煩..

有辦法用相對路徑的方式，指定輸入的位置與輸出的位置嗎?

當然可以!
首先在 `webpack.config.js` 用 `require` 的方式來引入 `path`

```
var path = require('path');
```
因為 node 要搬移檔案或叫出檔案的路徑都會靠 **path** 這個 api
然後在我們 `module.export` 的設定物件裡加入 **context** 這個屬性，使用 `path.resolve` 指名我們 input 的路徑

```
context: path.resolve(__dirname, '當前__dirname目錄下欲指定的資料夾位置'),
```

例如這樣
```
context: path.resolve(__dirname, './src'),
```

說明一下：

* **path.resolve( )**
可以將當前或指定位置的相對路徑解析合成絕對路徑

* **__dirname**
nodejs 中的特殊變數，會指向當前執行文件的完整資料夾位置

所以我們要設定 `path.resolve` 第一個參數 **__dirname**，指定第二個參數為 `src`
整個路徑就會從當前位置然後接續 `./src` 資料夾開始找檔案

這麼一來我們在 `entry` 就不用傻傻的寫出要 input 的檔案絕對路徑
直接從 context 解析後指定的路徑位置 `./src` 下開始查找 `index.js`

```
var path = require('path');

module.exports = {
    context: path.resolve( __dirname, "./src"),
    entry: './index.js',
    output: {
        filename: 'index-bundle.js'
    }
}
```

存檔後終端機輸入`npm run start`後報錯
![](https://i.imgur.com/n30wIl6.png)

因為我要編譯的 index.js 還放在根目錄
![](https://i.imgur.com/YaM8Oh5.png)

趕快創建一個 src 資料夾並把檔案移進去
終端機再次輸入`npm run start`後就成功了
webpack 將編譯後的檔案輸出到 dist 資料夾
![](https://i.imgur.com/I6LYW4R.png)

### output 輸出

..那，如果我們想要將輸出的檔案位置路徑，也指定到特定資料夾呢?

很簡單 一樣可以靠 **path** 做到
我們在 `webpack.config.js` 的 output 物件新增一行 path

```
output: {
    path: 欲編譯後的資料夾位置
    ...
```

當然 一樣可以用 `path.resolve( )`
```
path: path.resolve(__dirname, '當前__dirname目錄下欲指定的資料夾位置'),
```

也就是變成這樣
```
var path = require('path');

module.exports = {
    context: path.resolve( __dirname, "./src"),
    entry: './index.js',
    output: {
        filename: 'index-bundle.js',
        path: path.resolve( __dirname, "./myBuild"),
    }
}
```
終端機輸入 `npm run start` 後
就成功編譯在我自己指定的 myBuild 資料夾囉
![](https://i.imgur.com/uMH7vBO.png)

