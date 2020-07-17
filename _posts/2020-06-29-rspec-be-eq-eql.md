---
layout: post
title:  "RSpec 中 eq、eql、be 的差別"
date:   2020-06-29 22:57:02 +0800
categories: jekyll update
---

這次利用 Astro Camp 提供給學員在結業後練功的教材 [18 銅人](http://xn--wcvr7bt7zuhhkmc/)，在第八步驟目標是為 Tasks 這個 Controller 內的 CRUD 寫測試。

<br>

### 寫 RSpec 的基本架構：掌握 AAA 原則

> 三個 A 分別是：Arrange, Act, Assert

**Arrange：**

- 建立初始化物件、及需要使用到的參數
- 白話文：安排一個實體

**Act：**

- 執行呼叫要用到的方法
- 白話文：請實體用一個 method 戳戳看、打打看

**Assert：**

- 驗證測試結果
- 白話文：查看測試結果，是否符合預期

以 `GET #index` 為例：

```
RSpec.describe "Tasks", type: :request do
  describe 'GET #index' do
    it "should assign all tasks to @tasks" do

      ## Arrange
      Task.create(
        title:'寫測試',
        description:'寫新增任務測試',
        status: '待處理',
        priority: '高'
      )

      ## Act
      get tasks_path

      ## Assert
      expect(assigns(:tasks)).to eq [task]
    end
  end
end
```

<br>

### RSpec 中 eq、eql、be 的差別

稍微了解了 RSpec 的撰寫架構後，對 `expect` method 中的 `eq` `eql` `be` 三者的差異有了一些好奇。

[RSpec 官方文件](https://relishapp.com/rspec/rspec-expectations/docs/built-in-matchers/equality-matchers)這麼寫：

```
a.equal?(b) # object identity - a and b refer to the same object
a.eql?(b)   # object equivalence - a and b have the same value
a == b      # object equivalence - a and b have the same value with type conversions
```

<br>

### 以下分別呈現使用 eq 、eql、 be 會得到的結果：

當無法快速理解官方文件的說明時，直接試一試是個好方法。

在 CRUD 的例子裡，以上方 `GET #index` 為例：

- 使用 `eq`

```
expect(assigns(:tasks)).to eq [task]

#預期的結果
expected #<Array:70270682497680> => [#<Task id: 442, title: "寫測試", description: "寫新增任務測試", status: "待處理", priority: "高", start_at: "2020-...6 00:44:00", deleted_at: nil, created_at: "2020-06-28 15:21:58", updated_at: "2020-06-28 15:21:58">]

#得到的結果
got #<ActiveRecord::Relation [#<Task id: 442, title: "寫測試", description: "寫新增任務測試", status: "待處理", priori..., created_at: "2020-06-28 15:20:34", updated_at: "2020-06-28 15:20:34">]>
```

<br>

- 使用 `eql`

```
expect(assigns(:tasks)).to eql [task]

#預期的結果
expected [#<Task id: 427, title: "寫測試", description: "寫新增任務測試", status: "待處理", priority: "高", start_at: "2020-...6 00:44:00", deleted_at: nil, created_at: "2020-06-28 15:21:58", updated_at: "2020-06-28 15:21:58">]

#得到的結果
got #<ActiveRecord::Relation [#<Task id: 427, title: "寫測試", description: "寫新增任務測試", status: "待處理", priori..., created_at: "2020-06-28 15:20:34", updated_at: "2020-06-28 15:20:34">]>
```

<br>

- 使用 `be`

```
expect(assigns(:tasks)).to be [task]

#預期的結果
expected #<Array:70270682497680> => [#<Task id: 442, title: "寫測試", description: "寫新增任務測試", status: "待處理", priority: "高", start_at: "2020-...6 00:44:00", deleted_at: nil, created_at: "2020-06-28 15:21:58", updated_at: "2020-06-28 15:21:58">]

#得到的結果
 got #<Task::ActiveRecord_Relation:70270681743480> => #<ActiveRecord::Relation [#<Task id: 442, title: "寫測試", description: "寫新增任務測試", status: "待處理", priori..., created_at: "2020-06-28 15:21:58", updated_at: "2020-06-28 15:21:58">]>
```

<br>

### 試試後發現：

```
a.equal?(b) # object identity - a and b refer to the same object
a.eql?(b)   # object equivalence - a and b have the same value
a == b      # object equivalence - a and b have the same value with type conversions
```

以下借用 [Stack Overflow](https://stackoverflow.com/questions/32926817/rspec-eq-vs-eql-in-expect-tests/32926980) 上的例子，希望有助於理解：

1. **`equal` 指的就是 `be`**
- 表示 a 與 b 的 `object_id` 要完全相同，也就是兩個是同一個 object，指向的記憶體位置是同一個。
- value 及 資料型態 都要相同。

```
expect(:my_symbol).to equal(:my_symbol)
# passes, both are identical.
```

```
expect('my string').to equal('my string')
# fails, objects are equivalent but not identical
```

```
expect(5).to equal(5.0)
# fails, Objects are not equivalence and not identical
```

**2. `eq` 指的就是 `==`**

- 表示 a 與 b 的 `object_id` 可以不同。在資料型態轉換後，value 相同。

```
expect(:my_symbol).to eq(:my_symbol)
# passes, both are identical.
```

```
expect('my string').to eq('my string')
# passes, objects are equivalent 
```

```
expect(5).to eq(5.0)
# passes, Objects are not equivalent but was type casted to same object type.
```

**3. `eql`**

- 表示 a 與 b 的 `object_id` 可以不同。不做資料型態轉換，只在乎 value 相同。

```
expect(:my_symbol).to eql(:my_symbol)
# passes, both are identical.
```

```
expect('my string').to eql('my string')
# passes, objects are equivalent but not identical
```

``` 
expect(5).to eql(5.0)
# fails, Objects are not equivalence, did not tried to type cast
```

<br>

### 小結

回到一開始 `GET #index` 這個測試的例子。

在 `GET #index` 這個測試中，我們預期得到一個 Array 裡面帶有很多個 tasks，其中我們在乎的是 Array 裡面的 value。

不過，我們會從 Controller 請 Model 調閱資料，最後 Active Record 會把資料包裝成物件回來，並存取在 instance variable 中。例如：

```
def index
  @tasks = Task.all
end
```

在 RSpec 中得到的結果會是 Active Record 回傳的 instance，但這與預期的 Array 資料型態不同：

```
#<ActiveRecord::Relation [#<Task id: 427, title: "寫測試", description: "寫新增任務測試", status: "待處理", priori... 00:44:00", deleted_at: nil, created_at: "2020-06-28 15:20:34", updated_at: "2020-06-28 15:20:34">]>
```

其中只有 `eq` 會協助資料型態轉換後，再去比對 value 是否相同。所以，在 `GET #index`這個例子裡面，使用 `eq` 會是比較符合需求的。

透過這次的整理對 `eq` `be` `eql`有了更清楚的認識。`GET #index` 適合使用 `eq` 僅適用於本次設定的測試預期結果，未來要寫其他類型的測試仍然要評估預期結果為何，再來決定使用哪個 method。


---
參考資料：

- [Relish — Equality matchers](https://relishapp.com/rspec/rspec-expectations/docs/built-in-matchers/equality-matchers)
- [Stack Overflow — Rspec `eq` vs `eql` in `expect` tests](https://stackoverflow.com/questions/32926817/rspec-eq-vs-eql-in-expect-tests/32926980)