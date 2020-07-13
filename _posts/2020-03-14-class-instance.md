---
layout: post
title:  "Ruby - 類別與實體"
date:   2020-03-14 22:57:02 +0800
categories: jekyll update
---


### 什麼是類別（Class）？ 什麼是實體（Instance）？
我試著用 「Ikea 會員卡」 來比喻。

* 類別 = Ikea 會員卡，這張會員卡裡面「可以使用多項服務」，例如：點咖啡、買傢俱、吃晚餐。
* 實體 = Ikea 會員，凡是註冊的人就可以使用 Ikea 提供的服務。

＃換成程式碼來看看：
```
class Ikea
  def cafe
    puts "來一杯咖啡"
  end 
  def dinner
    puts "一份鮭魚馬鈴薯"
  end
end

lynn = Ikea.new      #lynn 已註冊為 Ikea 會員
p lynn.cafe          #印出「來一杯咖啡」
```

以上述程式碼為例，
* 類別 = Ikea（註：類別命名必須是常數，即首字需為英文字母大寫）
* 實體 = lynn

「類別」就像是「諸多服務的組合」，這些組合我們在 Rails 裡面稱之為「方法」。「實體」則像是「加入 Ikea 的會員」，透過 " Ikea.new" 指定給會員姓名，即產生一個新的實體。在 public method 的前提下，實體可以使用類別中的所有方法。

此外，在 Ikea 裡面，服務就是擺在那裡，需要有人來用服務才會啟動。從上面的程式碼來看，lynn 這個捧油已經是會員了，lynn 可以透過呼叫「方法」使用 Ikea 裡面的服務。 ex. 用lynn.cafe 來取得一杯咖啡。


---

### 如果實體想在 Class 之外「取得」或「指定」參數的值，可以怎麼做？

「實體」被 new 出來後，我們應該會想去取用「類別」（設計圖）裡面的「方法」（組件）。此時，可以透過「呼叫方法」的方式來取得。ex. `kitty.say_my_name`

![](https://i.imgur.com/BRUYSth.png)


不過，如果「方法」還沒有被建立，但又需要取得「實體變數」指定的「參數的值」，可能就會遇到困難。 例如：

1. 不能使用 `p kitty.name`：因為本身就沒有 name 方法
2. 不能使用 `p kitty.@name`：`@name` 是實體變數，不是方法
3. 不能使用 `p @name`：這樣沒有實體對象，程式會不知道你要給誰。


![](https://i.imgur.com/W55Ri5g.png)


所以，龍哥的教學中，才會開始一步一步帶：

#### 原則：沒有那個方法，就建給它

1. 下 `p kitty.name` 指令時，系統會顯示 `undefined method`。 => 那我們就建立一個 name 方法給他：

```
def name
  return @name
end
```
=> 這段稱之為 `getter`


2. 設定好 1. 之後，如果這樣寫 `kitty.name = "kao"`，系統會顯示 `undefined method "name="`

因為 `kitty.name = "kao"` 的原型其實是：`kitty.name=("kao")`，這裡的「方法」是 `name=()`。 等於是另一個方法。 如果沒有，就做給它：

```
def name=(new_name)
  @name = new_name
end
```
=> 這段稱之為 `setter`


3. 這時候下 `p kitty.name` 與 `kitty.name = "kao"` 就都能正常運行了

![](https://i.imgur.com/pAonbKB.png)


---

不過，又不過ＸＤ，從設定 getter 到 setter 的過程真的太煩了，所以有了另一個魔法：
* attr_reader = getter 的這段 code
* attr_writer = setter 的這段 code
* attr_accessor = getter + setter 的 code

寫法如下：

![](https://i.imgur.com/B11bbak.png)


*** 助教提醒 ***
`attr_reader` 只能產出：

```
def name
  return @name
end
```
`attr_writer` 只能產出：

```
def name=(new_name)
  @name = new_name
end
```

=> 也就是說，如果今天功能較複雜，def 裡面的實作內容相對也會較複雜。 這時候 `attr_reader` `attr_writer` `attr_accessor` 就不管用囉！
