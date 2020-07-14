---
layout: post
title:  "Ruby - Ruby Object Model"
date:   2020-03-29 22:57:02 +0800
categories: jekyll update
---

## 一、為什麼要了解 Ruby Object Model?

這需要從 Ruby 中的「模組」(module) 談起。

「模組」的用途是協助我們將需要「重複使用」到的程式碼打包起來，當某個「類別」(class) 需要使用時，只需要 `include` 或 `extend` 進來即可。

這樣的好處是，如果有多個「類別」都需要使用同一組程式碼，就不用每個類別都寫一次這組程式碼；此外，用 `include` 或 `extend` 的方式引進「類別」，隨時可以「拔除」模組，而不會影響到「類別」裡面原有的功能。


書上有教，「模組」有以下兩個特性：
* 模組不能實體化

    ```ruby=
    module Flyable
    end

    Flyable.new    #這樣不行
    ```

* 模組沒有繼承功能

    ```ruby=
    module Eatable < Flyable
    end   #這樣也不行
    ```

也許會好奇：
> 1. 模組為什麼不能實體化？
> 2. 模組為什麼不能繼承？
> 3. 類別和模組究竟有什麼差別？

以上三個問題，我們可以從 **Ruby Object Model** 得到解答

<br>

## 二、Ruby Object Model 怎麼運作？

![](https://i.imgur.com/65IQi3f.png)


### 假設今天有個「貓類別」繼承至「動物類別」：

``` ruby=
class Animal
end

class Cat < Animal
end

kitty = Cat.new
```

未來若程式碼一多的時候，若想知道繼承至誰、是由誰生出來的實體時，可以下以下指令：

* 繼承至誰：ex. `Cat.superclass` => 即可查到是 `Animal`
* 實體是由誰 new 出來的：ex. `kitty.class` => 即可查到是 `Cat`

<br>

現在，開啟我們的好奇心，開始來探索 Ruby Object Model 的運作：

#### 往上展開好奇心，讓 `superclass` 帶領我們
* 往上想知道 Animal 是繼承至誰
    * 於 VScode 寫上 `p Animal.superclass`
    * 並在終端機執行此 rb 檔
    * 可以得到 `Object`：Animal 是繼承至 `Object`
* 那 `Object` 繼承至誰？
    * （重複上面的步驟）
    * 可以得到`BasicObject`：Object 繼承至 `BasicObject`
* 那 `BasicObject` 繼承至誰？
    * 可以得到`nil`：上面沒人了
<br>

#### 往右展開好奇心，讓 `class` 帶領我們
* 往右想知道 kitty 是被誰 new 出來的
    * 於 VScode 寫上 `p kitty.class`
    * 並在終端機執行此 rb 檔
    * 可以得到 kitty 是被 `Cat` new 出來的
* 那 `Cat` 是被誰 new 出來的？
    * （重複上面的步驟）
    * 可以得到 `Cat` 是被 `Class` new 出來的
* 那 `Animal` 是被誰 new 出來的？
    * 可以得到 `Animal` 是被 `Class` new 出來的(?!)
* 那 `Object` 是被誰 new 出來的？
    * 可以得到 `Object` 是被 `Class` new 出來的(?!!)
* 那 `BasicObject` 是被誰 new 出來的？
    * 可以得到 `BasicObject` 是被 `Class` new 出來的(?!!!)

所以，其實所有的「類別」(class) 的 class 是 Class !!!

#### 繼續往右、往上展開好奇心

* 那 `Class` 是被誰 new 出來的？
    * 可以得到 `Class` 是被 `Class` new 出來的。自己 new 自己？！

* 那 `Class` 繼承至誰？
    * 可以得到`Class` 繼承至 `Module` (齁?!)

* 那 `Module` 繼承至誰？
    * 可以得到`Module` 繼承至 `Object` (咦?!)

* 那 `Module` 是被誰 new 出來的？
    * 可以得到 `Module` 是被 `Class` new 出來的。


#### 再看一次這個圖，可以發現：

1. 所有的「物件」都有一個 class 方法
2. 其實所有的「類別」(class) 以及 Module 的 class 都是 Class
3. Module 雖然繼承至 Object，但其實 Object 是個空物件。當使用 `p Flyable.superclass` 時，訊息會出現 `undefined method 'superclass' for Flyable:Module`。 沒有 `superclass` 這個方法的意思。

![](https://i.imgur.com/hiHSsnQ.png)

<br>

## 三、整理一下：「類別」跟「模組」的關係

> 我們來回答一開始的好奇：
> 
> 類別和模組究竟有什麼差別？
> 模組為什麼不能實體化？ 模組為什麼沒有繼承功能？
> 模組為什麼不能繼承？



1.  **類別和模組究竟有什麼差別？**
 從運作圖我們可以發現，`Class` 是繼承於 `Module`，兩者其實是繼承關係（兩人原來是祖孫關係呀！）。



2. **兩人是繼承關係，那模組為什麼不能實體化？ 模組為什麼沒有繼承功能？**
    
    既然是繼承關係，我們就可以來找找看到底 `Class` 比 `Module` 多了哪些方法？
    * 從`p Module.instance_methods` 知道 `Module` 有哪些方法
    * 從 `p Class.instance_methods` 知道 `Class` 有哪些方法

        => 輸入：`p Class.instance_methods - Module.instance_methods`
=> 可以得到：[:allocate, :superclass, :new]
    
    發現了嗎？ 
    `Class` 比 `Module` 多了這三種方法 [:allocate, :superclass, :new]

    也就是說，`Module` 本身就沒有 `superclass` 、 `new`、`allocate` 方法。
    * Module 沒有 `new 方法`，就不能產出別的實體。
    * Module 沒有 `superclass` 方法，代表往上也找不到它繼承於誰。

    這就解釋了「為什麼模組不能實體化」、「為什麼沒有繼承功能」。
    
    3. 解釋：為什麼 `module` ex. Flyable 可以用 `include` 或 `extend` 掛載在 「類別（class）」裡：
    「模組」本身就沒有繼承於誰，所以掛載在任何 class 身上，並不會影響到原本 class 的功能。
    
    
 補充：
    如果想查出某個類別的祖宗十八代可以使用 ex.：`p Cat.ancestors`。 這可以連同繼承的類別及掛載的模組通通列出來。

<br>

## 四、模組(module) 和 繼承(inheritance) 的差別

「模組」是一種「物件」，而「繼承」是一種「動作」。

1. 從 Ruby Object Model 得知，「類別 Class」繼承於「模組 Module」，兩者是繼承關係。
2.  「模組」本身沒有繼承功能，而是用 `include` 幫「實體」 加上「實體方法」、用 `extend` 幫「類別」加上「類別方法」。（為類別裝上超人翅膀的概念）
3. 「類別」本身則具有繼承功能，可以透過以下寫法，來達到繼承的目的：

```ruby=
class Animal
end

class Cat < Animal  #Cat 類別可以使用 < 大於的符號表示繼承於 Animal
end
```

---

參考資料：
- Astro Camp 課程
- [為自己學 Ruby On Rails（類別與模組）](https://railsbook.tw/chapters/08-ruby-basic-4.html)

推薦文章：
- [Ruby 的繼承鍊 (1) - 如何實踐物件導向](https://www.spreered.com/ruby-object-model-1/)