---
title: Git experience with gitk
author: 春雨
categories: [Git]
date: 2019-10-25 23:08:47
tags: [git, gitk]
---

![](https://i.imgur.com/nl83IML.png)

前幾天把跟網友一起寫的專案用git上傳上去，礙於我不是很熟練指令，就跟照著網路上的教學把指令照打了進去，結果不小心pull到，我打的程式就被覆蓋過去了

後來基於朋友的幫助下又再次恢復了先前的branch，順便認知到git的重要性以及git指令的熟練度

而恢復原本的branch的方法則是用gitk這個工具來把git branch GUI化，一看就懂，非常方便!

![](https://i.imgur.com/9Ll3S88.png)

## 10/27更新:
附上錯誤的指令、倒退至原本branch的神奇方法、被GUI化的branches以及起承轉合

我從github上面pull了一個專案下來，但是寫好以後想要push到我的github的fork，在指令不熟的狀況下意外地把專案又pull下來一次，導致origin master覆蓋local branch(SS)

`出事後的branch們`

![](https://i.imgur.com/e4w3MDK.png)

`不應該用pull，會導致覆蓋local branch`

![](https://i.imgur.com/JL7lux4.png)

`倒退回brach的方法`
- 選定(checkout)想要被回朔的branch
- 複製想要回朔branch的sha1碼前幾位(4~7)
- git reset --soft [commit hash]

這樣就完成了
