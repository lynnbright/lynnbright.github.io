---
layout: post
title:  "ORM 與 Active Record 的關聯"
date:   2020-06-20 22:57:02 +0800
categories: jekyll update
---

### 什麼是 ORM ?

```ruby
#example
User.all
User.first
User.last
```

一般要與 Database 調閱資料，需要透過 SQL 語法來取得，例如：

`SELECT * FROM User WHERE name = "Linda" AND age > 16;`

不過，實務上資料庫查詢的需求多變，有些是一次性、有些是需要常態性查詢，如此一來需要寫大量的 SQL 語法，會導致不好維護和管理。而 ORM 某種程度解決了這個問題。

**ORM (Object-relational mapping) 是一種將資料庫的資料轉換成物件的機制。Rails 中的 Active Record，就是 ORM 的實作品**。

<br>

### 什麼是 Active Record ？

Active Record 代表的即 MVC 的 M (Model)，用來處理商業邏輯及負責操作、新增資料。

網站開發前期規劃我們會先設計一版資料結構，資料結構指的就是需要哪些資料表、資料表需要哪些的欄位、哪些資料表之間需要有關聯性。

依照資料存取需求，會產生多張資料表（即 Table），Table 與 Table 之間有可能產生一對一、一對多、多對多的關聯，而 Table 之間可以有共同的欄位值，方便到時候找到需要的某幾筆資料。這樣的資料儲存模式稱之為「關聯性資料庫」（Relational Database）。

舉個多對多關聯的例子：

- 醫生：Table 名稱 Doctor
- 病人：Table 名稱 Patient
- 櫃檯：Table 名稱 Center

```
#醫生可以有很多個病人
class Doctor < ApplicationRecord
  has_many :centers
  has_many :patients, through: :centers
end
```

```
#病人可以找不同醫生看診
class Patient < ApplicationRecord
  has_many :centers
  has_many :doctors, through: :centers
end
```

```
#櫃檯會匯集醫生及病人的看診紀錄
class Center < ApplicationRecord
  belongs_to :doctor
  belongs_to :patient
end
```

<br>

假設今天想要找出 id 是 1 號的醫生，他在 6/24 有哪些病人要看診（最近一次預約看診日期欄位名稱：`meeting_date: string`），在 Rails 可以這樣向 Database 調閱資料：

```
#先找出1號醫生的資料
doctor_1 = Doctor.find(1)
```

```
#接著找出6/24的病人清單
doctor_1.patients.where(meeting_date = '2020/06/24'）
```

<br>

### Active Record 的角色

我們可以把 Active Record 想像成一個加工廠，當透過 Model 向 Database 調閱資料、Database 回傳資料時，都會經過 Active Record，經過時 Active Record 就會動態將一筆一筆資料轉換成物件（Object）。

![ORM](/assets/images/orm.gif)

(圖片引用自：[https://medium.com/@hidace/an-overview-of-object-relational-mapping-3255fa26d51f](https://medium.com/@hidace/an-overview-of-object-relational-mapping-3255fa26d51f))

這時候就可以將調閱回來的資料存在 Controller 中實體變數中，接續給其他 action 使用、或是放到 View 中顯示資料。

例如：`@articles = current_user.articles.order(created_at: :desc)`

回到剛剛提到：「寫大量的 SQL 語法，會導致不好維護和管理」的問題。這也是為什麼在 Rails 中，我們會將 Model 的 Class 繼承至 Active Record 的原因。有了 Active Record，Class 就可以直接使用 Active Record 去存取資料。


---

參考資料：

- [ORM in Ruby: An Introduction](https://www.sitepoint.com/orm-ruby-introduction/)
- [An Overview of Object Relational Mapping && ActiveRecord](https://medium.com/@hidace/an-overview-of-object-relational-mapping-3255fa26d51f)
- [資料庫與Object Relational Mapping](https://medium.com/@vicxu/%E8%B3%87%E6%96%99%E5%BA%AB%E8%88%87object-relational-mapping-316cc5aaae7d)