---
layout: post
title:  "如何在 Rails 專案安裝 Stimulus"
date:   2020-05-10 22:57:02 +0800
categories: jekyll update
---

JS 的微框架，讓你可以局部控制 JS
* 官方英文版介紹：[https://stimulusjs.org/](https://stimulusjs.org/)
* 繁體中文版介紹：[https://www.gitbook.com/book/andyyou/stimulusjs](https://www.gitbook.com/book/andyyou/stimulusjs)


### 安裝方式有兩種：

1. `$ rails webpacker:install:stimulus`
2. 採用官方的安裝步驟

### 方法一：使用 rails 指令安裝
`$ rails webpacker:install:stimulus`

:::info
:bulb: tips
既然已經有先安裝 `webpacker`，stimulus 安裝手冊也有提到可以用 `webpack` 來載入，就可以先在終端機先輸入 `rails`，可以看到
:::

![](https://i.imgur.com/C83Wfch.png)


### 好處
1. 會直接在 `/app/javascript` 新增一個 `controllers` 資料夾。
2.  `controllers` 資料夾會自動建好範例檔 `hello_controller.js` & `index.js`
3.  可以直接在 `localhost:3000` 執行


注意：安裝好後，你可能會遇到 Webpack Compile fail `Module not found: Error: Can't resolve 'stimulus' in....` 的問題，此時只要把你的 `rails server` 或 `foreman` 重新啟動即可。
 

### 方法二：採用官方步驟安裝
如果是直接採用官方的安裝步驟：
```
$ git clone https://github.com/stimulusjs/stimulus-starter.git
$ cd stimulus-starter
$ yarn install
$ yarn start
```

則會在專案中產生：
* `stimulus-starter` 裡頭有：
    * `public` 資料夾：裡頭有 `index.html` 範例檔
    * `node-module` 資料夾（推測是要用 Webpack 來打包，所以也載入了這個肥大的資料夾）
* `src` 資料夾：裡頭有 `controllers` 資料夾＆`hello_controller.js`,`example_controller.js`


一些思考：
我們在 rails new 專案時，已經有自動載入 Webpack，所以方法二也許不那麼適合，因為會額外再載入一次肥大的 node-module 資料夾。