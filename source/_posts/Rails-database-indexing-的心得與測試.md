---
title: Rails database indexing 的心得與測試
date: 2020-02-11 15:53:40
tags:
---
關於Database indexing的介紹與運作方式這邊請 https://medium.com/@jinghua.shih/rails-%E7%B6%B2%E7%AB%99%E6%95%88%E8%83%BD%E5%84%AA%E5%8C%96-%E4%BA%8C-%E8%B3%87%E6%96%99%E5%BA%AB%E7%B4%A2%E5%BC%95-database-index-bd89fa3757a

以下紀錄自己嘗試的結果：
試著從tasks這張table中撈出:topic => “test”的這筆資料，第一次測試，資料總共六筆。

```
Task.where(topic: 'test').explain
  Task Load (0.5ms)  SELECT "tasks".* FROM "tasks" WHERE "tasks"."topic" = $1  [["topic", "test"]]
 => EXPLAIN for: SELECT "tasks".* FROM "tasks" WHERE "tasks"."topic" = $1 [["topic", "test"]]
                      QUERY PLAN
-------------------------------------------------------
 Seq Scan on tasks  (cost=0.00..1.07 rows=1 width=120)
   Filter: ((topic)::text = 'test'::text)
(2 rows)
```

加上index

```
def change
  add_index :tasks, :topic
end
```

再撈一次試試看

```
Task.where(topic: 'test').explain
  Task Load (0.8ms)  SELECT "tasks".* FROM "tasks" WHERE "tasks"."topic" = $1  [["topic", "test"]]
 => EXPLAIN for: SELECT "tasks".* FROM "tasks" WHERE "tasks"."topic" = $1 [["topic", "test"]]
                      QUERY PLAN
-------------------------------------------------------
 Seq Scan on tasks  (cost=0.00..1.07 rows=1 width=120)
   Filter: ((topic)::text = 'test'::text)
(2 rows)
```

對比前後兩次結果，發現所需時間怎麼變久了呢？
那我們把資料數兩增加到一百筆呢？

這是沒有加index的結果

```
Task.where(topic: '鄔嘉宏').explain
  Task Load (0.6ms) 
 #SELECT "tasks".* FROM "tasks" WHERE "tasks"."topic" = $1.....
                      QUERY PLAN
-------------------------------------------------------
 Seq Scan on tasks  (cost=0.00..2.15 rows=1 width=120)
   Filter: ((topic)::text = '鄔嘉宏'::text)
(2 rows)
```

加了之後

```
Task.where(topic: '符雅芳').explain
  Task Load (0.5ms)  
SELECT "tasks".* FROM "tasks" WHERE "tasks"."topic" = $.....
                      QUERY PLAN
-------------------------------------------------------
 Seq Scan on tasks  (cost=0.00..3.23 rows=1 width=120)
   Filter: ((topic)::text = '符雅芳'::text)
(2 rows)
```

在資料數目不多的時候，加上index搜尋時（我猜測）會多查詢該欄的參照表格，反而要多查詢一次，因此速度較慢，但若資料數非常多的話，有加上index效能就會改善不少。
關於index有很多種不同的運作方式例如：B-tree index、GiST indexes、GIN但我的能力還不足以了解使用時機跟方法，日後回顧這篇文章時再將其補完。

參考資料：https://karolgalanciak.com/blog/2018/08/19/indexes-on-rails-how-to-make-the-most-of-your-postgres-database/
https://xguox.me/rubyist-guide-to-postgres-explain.html/
https://bamboolab.eu/blog/rails-postgresql-index-btree-gin-gist
https://medium.com/@jinghua.shih/rails-%E7%B6%B2%E7%AB%99%E6%95%88%E8%83%BD%E5%84%AA%E5%8C%96-%E4%BA%8C-%E8%B3%87%E6%96%99%E5%BA%AB%E7%B4%A2%E5%BC%95-database-index-bd89fa3757a