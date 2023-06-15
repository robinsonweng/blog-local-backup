---
title: 用asyncio以前，你需要注意的事情
author: 春雨
categories: ['Python']
date: 2022-01-08 13:08:47
tags: [corutine, asyncio]
---

## 1. asyncio只不過是python coroutine的一種實現方法

掌握寫coroutine的好處與壞處，實作的時機，雷點以及隱憂，如何排錯，遠遠比用甚麼炫泡函式庫無腦加上async/await還要重要上好幾倍(對說的就是我)，除了asyncio，gevent，trio都可以去多看看人家的文檔，是怎麼實作出來的，跟其他模組差在哪邊

## 2. 任何事情都有代價

雖然async常常標榜不需要管理討厭的鎖、效能提升，但有很多東西是他們沒有提到，卻是寫出好coroutine的關鍵

不需要管理討厭的鎖
是，async的確不需要管討厭的鎖了，但作為代價，async的context switch 並不會像thread那樣有固定規律，並且由於async的實現方式只是由main thread管控context switch，沒有額外的限制就會出現像是 back-pressure 的問題。沒有固定規律要debug跟解決bug都是個難題。不要用了asyncio幻想自己「我只要用async就可以不用去理解thread, process, lock…」，只會在後面付出代價而已。

效能提升
前面的惡夢還沒結束，社群又多了一個奇怪的傳言「async會提升效能」這一論點，而這對我來說就是將程式語言本身作為解決問題的工具的癥結點。這點在 評論以前的文章 當中曾經有提到過:

{% colorquote info %}
程式語言不是解決問題的工具，他是用來做出解決問題的模型的工具，解決問題與否最終還是得取決於工程師本身
{% endcolorquote%}

如果用榔頭把釘子釘在錯的位置，椅子自然是不堪一擊。同理，專案並不會因為加入了coroutine或是asyncio就會變快，而是在經過評估，在正確使用的前提之下才會得到較多的好處，較少的壞處，就我所知，主要有以下幾個使用情境:

- 任務多為I/O類型，並且大部分時間在wait
- 不該有任務吃需要大量cpu計算
- 有一定程度的流量或是執行次數

並且影響效能的也不只與async/sync有關，以網頁為例，在django/flask上寫了async code，並不代表他在gunicorn/uwsgi上執行的效率會一樣，也不代表就可以用效率差的方式去query database。而且很多框架在benchmark的時候並沒有詳細交代參數與環境變因，數字看起來很棒其實也沒啥用

![](https://i.imgur.com/vU11HSS.jpeg)

[source](https://lumen.golaravel.com/)

## 後記

重新審視我對coroutine的看法讓我有新的發現，但整理完這些資料讓我想到新的問題: 「asyncio會影響到TCP/IP的握手效率嗎?」

2022-01-09 update:
答案是不會，以django為例，在web app收到request的時候，他已經是第四次握手(傳輸資料階段)了

2022-01-24 update:
有熱心路人提醒，coro是否會提升Http握手效能還會取決於Http的版本，例如 Http/1.1支援 keep alive， 而 Http/1.0不支援，所以在Http/1.0上使用coro的資源會比Http/1.1還多。
