---
layout: post
title:  "Webpack 與 Webpacker 在 Rails 的運作方式"
date:   2020-04-12 22:57:02 +0800
categories: jekyll update
---

近期開始嘗試以 Rails 架設簡單的訂便當系統，因為希望透過 Bootstrap 的格線系統讓畫面排版更省力一些，因而接觸到了 Webpack 與 Webpacker 的概念。 一開始在了解兩者的關係時，腦內大概是一團毛線球的狀態，Webpack 是什麼？ 與 Webpacker 的差異是什麼？ Rails 跟兩者的角色關係是什麼？為什麼 Rails 需要它們？  為什麼一開始建立專案時就已經有了 `node-module` 資料夾？ 要怎麼使用 Webpack？

為了讓腦內毛線球順一些，於是展開了梳理概念的動作。 
<br>

### 一、什麼是 Webpack

**目前瀏覽器的解析只看得懂三個東西：HTML、CSS、Javascript。**
不過，隨著前端技術的演進，出現了許多的 Preprocess(前處理)工具跟框架，例如：Vue, React, Sass, pug…等。

不過這些 Precprocess 工具與框架寫出來的東西，瀏覽器無法辨識（看謀啊～～）。 既然瀏覽器看不懂，又希望實現 Preprocess(前處理)工具與框架寫出來的東西，那我們叫個人來把這些東西「翻譯」好成瀏覽器看得懂的內容，再「打包」拿給 server，然後再提供給瀏覽器，總可以了吧？

可以～ 目前前端的世界有許多自動化的工具在做檔案編譯與打包，但因為各個工具有其好壞，Webpack 是其中公認較穩定好使用的工具，所以成了現在的主流。

所以，簡單來說，**Webpack 是一種自動化編譯及打包的工具。它幫我們把檔案編譯成瀏覽器看得懂的內容，並打包好上傳到 server，再提供給瀏覽器去解析。**

<br>

### 二、為什麼 Rails 需要 Webpack

可以分成廣義跟具體原因來理解：

* **廣義原因：**
承如第一點提及，瀏覽器目前只看得懂三個東西：HTML、CSS、Javascript。
我們在寫 Rails 專案時，例如：設定格式驗證功能，若使用者填錯格式，會跳出紅色框提醒。 這個紅色框我們可能會寫在 `.scss` 檔案裡，不過瀏覽器無法解析 `.scss` 檔案啊～～ 所以，我們需要將這個 `.scss` 檔案指定給 Webpack（怎麼指定？ 後面會提到），請它幫我們「編譯」＋「打包」然後丟給 server 上傳。


* **具體原因：各取所長，前端的東西交給效能較好的 Webpack** 
Rails 原生有一個 Assets Pipline 負責翻譯打包`.scss` 這類檔案，但當專案越來越大時，Assets Pipline 的運作速度就會越來越慢。  慢下去不是辦法，從 Rails 6 開始，當 `rails new` 一個新專案時，Rails 會自動啟動 Webpacker Gem 把 Webpack 所有相關套件建立好，並歸至 Rails 專案中。 
因為 Webpack 是在 Node.js 上運作，若專案中寫了諸多前端熱門框架
（ex. Vue, React 等），編譯打包前端資料的效能會比 Rails 的 Assets Pipline 還好。  不過，若專案只有比較簡單的 CRUD 功能（Rails 擅長的地方）， Assets Pipline 的效能還是可以表現的不錯。


注意：Webpack 是在 Node.js 上運作，所以要使用 Webpack 的話，電腦必須先安裝 Node.js 唷！（[Node.js 官方載點](https://nodejs.org/en/download/)）

<br>

### 三、Rails 自動安裝 Webpacker 過程中發生了哪些事


![](https://i.imgur.com/vcxY3uj.png)




#### 1. 什麼是 Webpacker

Webpacker 是一種 Ruby Gem。當 `rails new` 專案時，Rails 會自動安裝 Webpacker，Webpacker 則會幫忙安裝一切 Webpack 工具所需的檔案。




#### 2. Webpacker 自動做了哪些事

(1) 執行 `$ npm init` 指令

* 目的：產生一個 `package.json` 檔。
* 說明：`package.json` 檔包含了專案資訊、套件版本資訊、dependencies 等。

![](https://i.imgur.com/LKOGMvw.png)

<br>

(2) 執行 `$ npm install`  或 `$ yarn` 指令
* 概念：和 `$ bundle install` 相似 （複習：[ 什麼是 bundle install](https://medium.com/lynn-%E7%9A%84%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98/rails-%E6%96%B0%E6%89%8B%E6%9D%91-bundle-install-%E5%92%8C-gem-install-%E7%9A%84%E5%B7%AE%E5%88%A5-bd416ee8b2eb)）
* 目的：
    * 讀取`package.json` 檔。
    * 到 yarn 套件管理庫去下載 `package.json` 檔中有列出的所有套件。
    * 將載回來的套件放到 `node-module` 資料夾。
    * 產生一份 `package-lock.json` 檔。


:::success
:bulb: **`package.json` v.s `package-lock.json` 角色差異**

* **`package.json`**：供 `$ yarn` 指令執行時讀取，並得知需要從 yarn 套件庫載回哪些套件。
* **`package-lock.json`**：供電腦了解套件與套件之間、套件的相依套件之間的相依性與相容性，紀錄套件運作最合適的版本資訊。
:::


【注意】
上圖從 Webpacker Gem 到完成安裝 Webpack 都是在 Rails 內運作， 最後才請 Webpack 幫我們把檔案上傳到 server 上去，再丟給瀏覽器去解析。

<br>

### 四、在 Rails 中怎麼使用 Webpack


Webpack 的角色是幫我們編譯及打包檔案，既然要請人家幫忙，就得告訴它要幫忙「編譯及打包哪些檔案」。
就像是派快遞你要告訴快遞：在哪取件、送件地址、收件者是誰，類似的概念。
快遞「在哪取件」概念，在 Webpack 中，我們稱之為「進入點」。

<br>

#### 1. 指定一個進入點給 Webpack

一般來說，指定進入點的檔案會寫在 `webpack.config.js` 中。不過，Rails 比較特別一點，進入點的撰寫位置會放在 `webpacker.yml` 這個檔案裡。 以下擷取部分 `webpacker.yml` 的內容：

```ruby=
# Note: You must restart bin/webpack-dev-server for changes to take effect

default: &default
  source_path: app/javascript
  source_entry_path: packs             #source_entry_path 即進入點路徑。即會從 app/javascript/packs 資料夾打包。
  public_root_path: public
  public_output_path: packs           #public_output_path 即打包好的放置位置。
  cache_path: tmp/cache/webpacker
  check_yarn_integrity: false
  webpack_compile_output: true
```

我們來看看 `app/javascript/packs` 內的檔案長怎麼樣：
可以看到許多被 `require` 進來的套件或資料夾，這些就是我們要請 Webpack 幫忙編譯與打包的東西。

![](https://i.imgur.com/Rfq6jyc.png)


<br>

#### 2. 如果在 Javascript 資料夾內自己新增了資料夾與 `.scss` 檔案，怎麼讓 Webpack 知道要請它幫忙？

(1)首先，新增的資料夾要先 `require` 到 `app/javascript/packs/application.js` 這個檔案內，`require` 的方式：
```
require("資料夾名稱")    #資料夾名稱通常會用複數：ex. styles, scripts
```

(2)新增一個 `index.js` 檔。 Webpack 預設會先讀取 `index.js` 的檔案。
假設你把美化某個表單的語法寫在`user_form.scss`，那麼在 `index.js` 中就需使用 Javascript 的語法 `import` 將`user_form.scss` 引入。寫法如下：
```javascript=
import 'user_form.scss';
```

如此一來，Webpack 一層一層讀取時，就會知道「喔～`user_form.scss`」也需要我幫忙。



#### 3. 啟動 bin/webpack-dev-server 看看調整的成果


使用 `rails server` 會發現，儘管只是改一個字的顏色，瀏覽器重新整理的速度會變非～常～地～慢。
一樣的概念，各取所長，既然我們已經安裝了 webpack，就請能快速載入 js 檔案的 `bin/webpack-dev-server` 來編譯打包檔案。啟動後，會發現修改檔案後再重新整理瀏覽器的速度快非常多。

那這樣 `rails server` 可以關掉了嗎？ 不行，這樣 Rails CRUD 的功能就出不來了啦 XD



這樣要開兩個 server 很麻煩，所以我們可以靠另一個 Ruby gem => `foreman` 來幫我們解決這個問題，之後只需要執行：
`$ foreman start` 就可以一次開這兩個 server 囉！



<br>

### 五、安裝 foreman

1. 安裝步驟
(1) 執行指令 `$ gem install foreman`
（foreman Gem description page: [forman gem](https://rubygems.org/gems/foreman)）

(2) 新增 foreman 到 gemfile 檔案裡：`gem 'foreman', '~> 0.87.1'`
![](https://i.imgur.com/1uVe1tU.png)


(3) 在 根目錄，新增一個 Procfile 檔，並給予指令

```ruby=
bento: bin/rails server -p 3000      
webpack: bin/webpack-dev-sever
```
* 命名可依照需求自由命名。 
* 預設啟動的 port 會是 5000，不過 Rails server 預設使用的是 port 3000，所以後方會再加一個 `-p 3000`

![](https://i.imgur.com/ttweQMx.png)



（4）把現在開啟的 `rails server` & `bin/webpack-dev-server` 都關掉，並先重新執行 `$ bundle install`，再執行 `$ foreman start`，就完成嚕！


---
參考資料：
* [Webpack教學 (一) ：什麼是Webpack? 能吃嗎？](https://medium.com/i-am-mike/%E4%BB%80%E9%BA%BC%E6%98%AFwebpack-%E4%BD%A0%E9%9C%80%E8%A6%81webpack%E5%97%8E-2d8f9658241d)
* [Node.js 是什麼？跟 JavaScript 有什麼關係](https://tw.alphacamp.co/blog/node-js-and-javascript)
* [nodejs-wiki-book](https://github.com/nodejs-tw/nodejs-wiki-book/blob/master/zh-tw/node_npm.rst)
* [[Day-5] 用Yarn取代npm加速開發](https://ithelp.ithome.com.tw/articles/10191745)
* [如何在 Rails 使用 Webpacker（上）](https://kaochenlong.com/2019/11/21/webpacker-with-rails-part-1/)
* foreman Gem description page: [forman Gem](https://rubygems.org/gems/foreman)
* [foreman 安裝手冊](http://blog.daviddollar.org/2011/05/06/introducing-foreman.html)
