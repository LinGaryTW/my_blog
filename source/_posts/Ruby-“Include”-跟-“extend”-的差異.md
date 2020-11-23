---
title: Ruby “Include” 跟 “extend” 的差異
date: 2020-02-08 15:29:02
tags:
---
```
module Run
  def run_and_jump
    p 'I\'m tired'
  end
end
class Man
  include Run
end
class Child
  extend Run
end
Man.new.run_and_jump    #"I'm tired"
Child.new.run_and_jump  # NoMethodError
Child.run_and_jump      #"I'm tired"
```

先做一個Run模組，分別把他include 還有extend到兩個class上
透過上面的測試可以發現，`include`代表Man 類別需要`new`一個新的物件實體，然後才能使用 include掛上去的方法。

extend則是在Extend掛上去的類別中使用它，可以直接把方法拿過來當成class method。

最後我做了個小實驗，掛上去的方法能被繼承嗎？

```
class Baby < Child
end
class Baby2 < Man
end
Baby.run_and_jump       #"I'm tired"
Baby2.new.run_and_jump  #"I'm tired"
```

看來是蠻順利的～