---
title: Rails 中的Scope
date: 2020-02-08 15:45:15
tags:
---

在rails 中要查詢資料表時，我們常常用where, find, find_by，或是自己寫class method，如果碰到要求特別的情況也可以用Scope來客製化自己的查詢方法，Scope會回傳ActiveRecord::Relation 或是 nil 所以還可以串接使用，讓程式碼更簡潔。

```
class Article < ApplicationRecord
  scope :published, -> { where(published: true) }
end
```

scope還可以把scope包進去

```
class Article < ApplicationRecord
  scope :published,               -> { where(published: true) }
  scope :published_and_commented, -> {    published.where("comments_count > 0") }
end
```

如果希望scope的目標是全部

```
class Client < ApplicationRecord
  default_scope { where("removed_at IS NULL") }
end
```

那scope跟自己在model中客製化的class method相較起來除了能讓程式碼更簡潔之外還有什麼不同呢？這邊來做個小實驗

```
class User < ApplicationRecord
  scope :by_name_v1, -> name { where(name: name) if name.present? }
  scope :recent_v1, -> { order("users.updated_at DESC") }
  def self.recent_v2
    order("users.updated_at DESC")
  end
  def self.by_name_v2(name)
    where(name: name) if name.present?
  end
end
User.by_name_v1(nil).recent_v1
#User Load (0.7ms)  SELECT "users".* FROM "users" ORDER BY users.updated_at DESC LIMIT $1    看起來沒事
User.by_name_v2(nil).recent_v2
#NoMethodError (undefined method `recent_v2' for nil:NilClass)
Bang!爆炸了 為什麼呢？
```

其實這段改成這樣寫兩個結果就一樣了

```
def self.by_name_v2(name)
  if name.present?
    where(name: name)
  else
    all
  end
end
```

我的心得是，如果在自訂類別方法時，要注意是否會回傳nil，否則它就不能像scope一樣能串接其他方法進行多重查詢
參考資料：http://blog.plataformatec.com.br/2013/02/active-record-scopes-vs-class-methods/

https://guides.rubyonrails.org/active_record_querying.html