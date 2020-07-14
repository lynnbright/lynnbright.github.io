---
layout: post
title:  "Rails - bundle install 和 gem install 的差別"
date:   2020-04-11 22:57:02 +0800
categories: jekyll update
---


### bundle install

每個 rails 專案都會有自己的 gem 套件，但是同一套件會有中多版本，版本間容易有相容性、或不同套件具有相依性的問題。

![](https://i.imgur.com/GfYlBSF.png)


**若要避免相容性、相依性而衍伸的（要除更多 bug）問題，bundle install 將是拿到任何 Ruby 專案，第一件要做的事 。**


`$ bundle install` 會根據專案中的 Gemfile 去讀取專案有哪些 Gem，接著到預設的 https://rubygems.org 自動下載及安裝這些 Gem 套件，並產生 Gemfile.lock 的檔案。

Gemfile.lock 的角色則是紀錄最新 Rails 需要的套件，Rails 啟動時就會去讀 Gemfile.lock 檔，去找對應的套件。

![](https://i.imgur.com/qTHBEnP.jpg)



### bundle install v.s gem install 的差別
根據上圖，
bundle install 會做兩件事：
* 去讀取 Gemfile 有哪些 Gem，到 https://rubygems.org 下載這些 Gem。
* 更新最新的 Gem 版本到 Gemfile.lock 。

gem install 則只會做一件事：
* 若指令是 gem install devise(Gem 名稱) ，gem install 會做的事只有到 https://rubygems.org 下載 devise 這個 Gem。

<br>

### 實作筆記（2020.05.04 更新）
當在專案中要安裝套件時，如果 command line 下 `gem install` 套件名稱 ，只會安裝到電腦，但不會安裝到專案中；若要安裝到專案中， 需先將 gem 新增到 Gemfile 中，再下 `bundle install` 或是 command line 直接下 `bundle install 套件名稱` 專案才可以用。 不然進到 rails console 要測試套件時，可能會得到類似 `NameError (uninitialized constant XXX)` 的錯誤訊息。

<br>


### 套件管理：Gemfile 安裝套件與版號的ruby檔

`～`表：找最接近的版號。（通常是抓最小跳動版號最接近值）
  - e.g ~> 3.1.7 就不會抓 3.2.0 版本
![](https://i.imgur.com/LkyJepJ.png)

<br>

### 補充：bundle update
若只要更新單一個 Gem，可以這樣下 command line：
```$ bundle update 套件名稱```
如此一來，Rails 就會幫忙將這個 Gem 以及它的相依套件更新到最新的穩定版本，並把版本資訊寫進 Gemfile.lock 中。


---

參考資料：
* [Day 04 Gem與Bundler](https://ithelp.ithome.com.tw/articles/10217717)


