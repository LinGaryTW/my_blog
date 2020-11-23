---
title: 初心者對於Ruby中符號(symbol)的感受
date: 2019-11-20 12:01:49
tags:
---

符號、字串、變數在剛接觸到Ruby時實在讓人非常困惑，這邊先簡單比對符號跟字串的特性，再讓變數跟他們互動一下。<br>
再Ruby中，變數、字串、符號都是一種物件，符號就跟數字一樣會是不可變的物件，字串則是可變動的物件，如下：<br>
![](https://miro.medium.com/max/830/1*TCD72XeB5bmT7Ag3bVfkOA.png)
我們確認三次”aaa”的object_id(記憶體之海中的位置)，發現每次都有所不同，在新手如我還不了解大多數功能時，看起來就是個很彈性能夠任意調整內容的東西，就算我沒在終端機修改任何字元，Ruby也都會運用不同的記憶體來面對新的指令。

接下來我們看看數字跟符號。

![](https://miro.medium.com/max/614/1*AAPqDxBxIpYe4TIkVhaIWw.png)

符號跟數字一樣都是用相同的記憶體欸，感覺就像老早就刻好放在那裡剛好被object_id檢查到顯示出來（數字確實是有固定規律2n+1)。
從上面的小檢測可以看出“符號”vs“字串”就是“不可變動的值”vs“可變動的值”
說到“值”就想到最初使用irb時都會打

![](https://miro.medium.com/max/588/1*vTW0MiMp-81msbrUcrcuiw.png)

這裡a就是指向“1”這個值，a本身是變數沒有被賦予意義，要有一個值才會存在。
最後提到符號的應用，因為符號有不可變的特性，所以適合拿來作為固定標示，如雜湊(Hash)的 Key。
舉個例子：profile = { name: ‘Gary’, height: ‘not_tall’ } 這個Hash如果沒有穿語法糖衣長這樣profile = {:name => ‘Gary’, :height => ‘not_tall’ }
看起來就是用定型的key（符號）去得到value吧～～
