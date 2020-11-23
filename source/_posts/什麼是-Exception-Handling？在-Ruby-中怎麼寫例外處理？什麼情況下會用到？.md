---
title: 什麼是 Exception Handling？在 Ruby 中怎麼寫例外處理？什麼情況下會用到？
date: 2020-02-08 15:33:15
tags:
---
這邊先簡單舉個例子，看看begin , rescue, raise, ensure 怎麼運作

```
a = 0
begin
  puts a
  if a < 5
    raise   # 引發例外，進入 rescue
  else
    puts "#{a} is bigger than 4"
  end
rescue
  a += 1
  retry
ensure
  p "finished"
end
#0
#1
#2
#3
#4
#5
#5 is bigger than 4
#"finished"
```

放在 `begin` 和 `end` 這兩個關鍵字中的方法或程式區塊（block）都可以使用 `rescue` 來攔截例外狀況。在測試的過程中，我們可以使用 `raise` 來引發錯誤，讓它進入到 `rescue` 中：

* `retry`：當你在 `rescue` 區塊中捕捉到例外時，可以使用 `retry` 來再次從 `begin` 開始執行。
* `raise`： 這個方法可以引發一個例外（exception），它會打斷原本程式執行的流動，例如使用 `raise`。
* `else`： 會寫在 `rescue` 後， `ensure` 之前，當程式碼的主體（`begin` 中的內容）沒有被引發例外的話，`else` 就會被執行。
* `ensure`： 則可以確保某段程式碼一定會在區塊的最後被執行，不論例外是否有被引發（`raise`）。

在rails 中碰到問題可以這樣客製化例外處理，比如說找不到資料或是尚未有任何資料，就可以用rescue回傳“請新增資料”

```
begin
  # do something dodgy
rescue ActiveRecord::RecordNotFound
  # handle not found error
rescue ActiveRecord::ActiveRecordError
  # handle other ActiveRecord errors
rescue # StandardError
  # handle most other errors
rescue Exception
  # handle everything else
  raise
end
```

這邊舉個例子

在rails 建立一個model Task
class Task < ApplicationRecord
  def self.find_task(id)
    find(id)
  rescue ActiveRecord::RecordNotFound
    p "no this task "
  end
end
假設目前沒有Task 預設Task.find(1)  #ActiveRecord::RecordNotFound 
用例外處理的方法 Task.find_task(1) #"no this task "


參考資料：https://pjchender.github.io/2018/06/05/ruby-error-handling%EF%BC%88%E9%8C%AF%E8%AA%A4%E8%99%95%E7%90%86%EF%BC%89/

https://stackoverflow.com/questions/3694153/catch-all-exceptions-in-a-rails-controller