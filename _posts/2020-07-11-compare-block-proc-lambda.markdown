---
layout: post
title:  "[比較] Ruby - Block, Proc, lambda"
date:   2020-07-11 22:57:02 +0800
categories: jekyll update
---


## Block

在 Ruby 的世界裡幾乎所有東西都是物件，但有少數例外不是物件， `Block` 就是其中一個。

- **特性**
    - `Block` 無法單獨存在
    - `Block` 必須依附在 method 身上才能有作用
    - `Block` 最後一行的執行結果也會自動變成 Block 的回傳值
    - `Block` 不能使用 `return` 。因為 `Block` 本身不是 method，在 `Block` 裡面寫 `return` 會不知道要 `return` 到哪裡去。

- **如何使用 `Block` - 透過 `yield`**

    當程式碼執行到 `puts lunch` 時，會進一步跳到 `def lunch ... end` 中，而這個 method 中有一個 `yield` 。這時候控制權就會轉給 `block` 來執行 `puts 'I want to have some noodles'`。 `block` 執行結束後，再將控制權轉回給 `lunch` 這個 method 繼續執行接下來的程式碼。

{% highlight ruby %}
def lunch
  yield
end

puts lunch { puts 'I want to have some noodles' }
{% endhighlight %}

- **method 中有 `yield` ，在外面呼叫 method 但後方沒有 `Block` 時會噴錯**

{% highlight ruby %}
def lunch
  yield
end

lunch()   

#LocalJumpError (no block given (yield))
{% endhighlight %}

- **如果要讓 method 正常呼叫、正常執行，可以使用 `block_given?` 判斷呼叫的 method 後方有沒有接 `block` 。**

{% highlight ruby %}
def lunch
  yield if block_given?
end

lunch()                     # 正常運作，但不會顯示任何東西
lunch { puts 'meat' }       # 印出 meat
lunch do puts 'meat' end    # 印出 meat
{% endhighlight %}

- Block 中的 `do...end` 和 `{} 花括號` 有什麼差別

  結論： `{} 花括號` 的優先順序大於 `do ... end`

{% highlight ruby %}
list = [1,2,3,4,5]

p list.map { |x| x * 2 }      # [2, 4, 6, 8, 10]
p list.map do |x| x * 2 end   # <Enumerator: [1, 2, 3, 4, 5]:map> #只執行到 p(list.map) 就結束了
{% endhighlight %}

---

## Proc

- 用途：將 `Block` 物件化。讓 `Block` 可以單獨存在。
- Proc 特性
    - 可帶參數
    - 在 Proc 中不建議使用 `return`
- Proc 物件化的方式：

{% highlight ruby %}
# 方法一
say_hello = Proc.new { |name| puts "hello! #{name}" }

# 方法二
say_hello = Proc.new do |name|
	puts "hello! #{name}" 
end
{% endhighlight %}


- Proc 呼叫方式 - 執行 Proc 物件化後的 `Block`

{% highlight ruby %}
say_hello = Proc.new { puts 'hello!' }

say_hello.call
say_hello.()      #記得括號前面有個小數點
say_hello []
say_hello ===
say_hello.yield
{% endhighlight %}


- Proc 可帶參數。但不會檢查代入參數的數量。

{% highlight ruby %}
say_hello = Proc.new { |name| puts "hello to #{name}" }

say_hello.call("Lynn")
say_hello.("Lynn")
say_hello["Lynn"]
say_hello.==="Lynn"
say_hello.yield "Lynn"
{% endhighlight %}

---

## lambda

- 用途：將 `Block` 物件化。讓 `Block` 可以單獨存在。
- 特性
    - 可以傳參數
    - 可以使用 `return`

- lambda 物件化的方式

{% highlight ruby %}
# 方法一
say_hello = lambda { |name| puts "hello! #{name}" }

# 方法二
say_hello = ->(name) { puts "hello! #{name}" }
{% endhighlight %}

- lambda 呼叫方法（和 Proc 一模一樣） - 執行 lambda 物件化後的 `Block`

{% highlight ruby %}
say_hello = lambda { puts "hello!" }

say_hello.call
say_hello.()
say_hello []
say_hello ===
say_hello.yield
{% endhighlight %}

- lambda 可帶參數，會檢查帶入參數的數量。

{% highlight ruby %}
say_hello = lambda { |name| puts "hello! #{name}" }

say_hello.call("Lynn")
say_hello.("Lynn")
say_hello ["Lynn"]
say_hello === "Lynn"
say_hello.yield "Lynn"
{% endhighlight %}

---

## 比較 Proc & lambda 的 `return`

- lambda 中可以使用 `return`

    當 lambda 內執行到 `return` 後，會將控制權交回給呼叫它的方法，繼續執行接下來的程式碼。

{% highlight ruby %}
def hello
  puts 'start'
  say_hello = -> (name) { return puts "hello to #{name}" }
  say_hello.call("Lynn")
  puts 'end'
end

hello
#印出
# "start"
# hello to Lynn
# "end"
{% endhighlight %}

- Proc 中不建議使用 `return`

    從下方看出來，在 Proc 中使用 `return` 的話，執行完 `block` 內的程式碼後，一切就結束了。

    接下來的程式碼，都不會被執行到。

{% highlight ruby %}
def hello
  puts 'start'
  say_hello = Proc.new { |name| return puts "hello to #{name}" }
  say_hello.call("Lynn")
  puts 'end'
end

hello

#印出 
# "start"
# hello to Lynn
{% endhighlight %}

### 比較 Proc & lambda 傳入參數的差異

- Proc 不會檢查傳入參數的數量，若不足則以 nil 補上。

{% highlight ruby %}
def hello
  puts 'start'
  say_hello = Proc.new { |name, age| return p "hello to #{name}" }
  say_hello.call("Lynn")
  puts 'end'
end

hello

#"start"
#"hello to Lynn"
{% endhighlight %}

- lambda 會檢查傳入參數的數量，若不足會報錯。

{% highlight ruby %}
def hello
  puts 'start'
  say_hello = -> (name, age) { return puts "hello to #{name}" }
  say_hello.call("Lynn")
  puts 'end'
end

hello
#wrong number of arguments (given 1, expected 2) (ArgumentError)
{% endhighlight %}

---

### 參考資料

- [The Ultimate Guide to Blocks, Procs & Lambdas](https://www.rubyguides.com/2016/02/ruby-procs-and-lambdas/)
- [[Ruby] 程式碼區塊（block）, Proc 和 Lambda](https://pjchender.github.io/2017/09/26/ruby-%E7%A8%8B%E5%BC%8F%E7%A2%BC%E5%8D%80%E5%A1%8A%EF%BC%88block%EF%BC%89-proc-%E5%92%8C-lambda/)
- [Ruby 中的 Block、Proc、Lambda 是什麼？](https://riverye.com/2019/11/15/Ruby-%E4%B8%AD%E7%9A%84-Block%E3%80%81Proc%E3%80%81Lambda-%E6%98%AF%E4%BB%80%E9%BA%BC%EF%BC%9F/)
