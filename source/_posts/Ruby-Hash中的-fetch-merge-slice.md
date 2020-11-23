---
title: Ruby Hash中的 fetch merge slice
date: 2020-03-04 16:05:37
tags:
---
## fetch
fetch可以讓我們想在hash中取值時，能更加客製化輸出結果
當我們給的key可能不存在時，例如

```
h = {a: 100, b:200}
h[:a] #100
h[:k] #nil
```

可以用h.fetch(:key, default output)
或是h.fetch(:key){block}
讓結果更有彈性。但若沒有給default如下面第二個測試，就會出現key not found 的error
用法如下

```
 h = {a: 100, b:200}
 h.fetch(:a) #100
 h.fetch(:k) #key not found: :k
 h.fetch(:k, 'see?') #see?
 h.fetch(:k){|e| "see? #{e}"} #see? k
```
參考連結 https://apidock.com/ruby/Hash/fetch

## merge

將兩個hash合併，若帶進去方法的hash有重複的key，就去更新對應到的值，並且回傳一個新的hash
假設我有兩個hash
```
a = {a: 10, b: 20}
b = {a: 30, c: 40, d: 50}
a.merge(b) # {a: 30, b: 20, c: 40, d: 50}
b.merge(a) # {:a=>10, :c=>40, :d=>50, :b=>20}
```
也可以用block去處理簡單的邏輯
```
a.merge(b) {|key, oldval, newval| newval - oldval}
# {:a=>20, :b=>20, :c=>40, :d=>50} 
```

在應用層面，假設今天去打了某個API，得到一坨jason，可以先用symbolize_keys 把所有東西轉成符號為key的hash，再用merge去做對應的邏輯處理，最後儲存到資料庫裡，類似這樣: Table.new(處理好的hash)

參考連結 https://apidock.com/ruby/Hash/merge

## slice

把對應到的key,value 切下來，組裝成新的hash

```
{ a: 1, b: 2, c: 3, d: 4 }.slice(:a, :b)
# => {:a=>1, :b=>2}
```

假設今天打了某之api,得到一坨json，就不這樣寫

```
respons = {data from api}

insert_data = {key1: respons[對應的key1], key2: respons[對應的key2]}
改成這樣寫
insert_data = respons.slice(對應的key1, 對應的key2)
```

參考連結 https://apidock.com/rails/Hash/slice