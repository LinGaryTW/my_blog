---
title: Block、 Proc 與 Lambda
date: 2020-01-29 15:24:50
tags:
---
在Ruby中，大部分東西都是物件，Block是少數的例外

Block 是什麼呢？？？
對於新手如我就是do…end或是{ …. }這種東西

```
#常見範例ㄧ
[1,2,3,4,5].each do |i|
  p i
end
#常見範例二
[1,2,3,4,5].each{|i| p i }
```

如果想要讓附掛在 methods 後的 Block 執行，可使用 yield 方法，暫時把控制權交棒給 Block，等 Block 執行結束後再把控制權交回來：

```
def say_hi
  yield  #這邊換成block開始動
  p "hello"
end
say_hi{ p "hi"}
#"hi"
#"hello"
```

那麼Proc 跟 Lambda呢～就是把block物件化然後能丟來丟去的神秘力量了～

```
Proc.new { p "hi" }
proc { p "hi" }
lambda { p "hi" }
->i{ p "hi" }
#<Proc:0x00007fbebe176ce8@(irb):14>
#<Proc:0x00007fbebe1c2968@(irb):15>
#<Proc:0x00007fbebe1c9c40@(irb):16 (lambda)>
#<Proc:0x00007fbebe1d98c0@(irb):17 (lambda)>
#丟到method
def say_hi(proc)
  proc.call
end
proc = Proc.new { p "hi" }
say_hi(proc) #"hi"
```

## lambda 和 Proc 的差異

lambda 和 Proc 幾乎一模一樣，除了：

lambda 會檢查代入的參數數目，Proc 不會。因此當丟入的參數數目不對時，lambda 會丟出錯誤，而 proc 只會忽略未預期的參數並代入 nil。

```
say_hello_lambda = lambda { |first_name, last_name| puts "Hello #{first_name}, #{last_name}."}
say_hello_proc = Proc.new { |first_name, last_name| puts "Hello #{first_name}, #{last_name}."}

say_hello_lambda.call('Aaron')  
# wrong number of arguments (given 1, expected 2)
say_hello_proc.call('Aaron')    
# Hello Aaron, .
```

當 lambda 內執行到 `return` 後，它會將控制權交回呼叫它的方法，繼續執行該方法後的片段；但當 Proc 內使用到 `return` 時，不會回到呼叫它的方法，而是立即跳出該方法：

```
def say_hi_if_in_good_mood_proc
  greeting = Proc.new { return "hi!" }
  greeting.call
  "I had a bad day"
end

puts say_hi_if_in_good_mood_proc        # hi!

def say_hi_if_in_good_mood_lambda
  greeting = lambda { return "hi!" }
  greeting.call
  "I had a bad day"
end

puts say_hi_if_in_good_mood_lambda # I had a bad day
```