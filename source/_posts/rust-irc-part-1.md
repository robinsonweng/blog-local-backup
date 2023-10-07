---
title: Implment irc server in rust part 1
author: 春雨
tags:
  - rust
categories: [rust]
date: 2023-10-07 14:45:51
---


原本是想要寫一個 DNS 的, 後來我朋友跟我推薦他學校的網路程式作業, 裡面就包含了 DNS, 所以想說乾脆就先從簡單的作業開始

## framework
除了 std libary 以外啥都沒用, 一來是因為只是要用於作業, 練習如何看規格文件, 練習寫 rust 等等, 二來是減少專題的複雜程度, 三來為了避免以前在學程式語言的錯誤: 用了功能太過複雜的框架, 導致學不到東西, 浪費很多時間, 結果一場空, 既沒有學到技術, 也沒有學到基礎。這樣寫了也快兩週了, 不得不說這個決策是正確的, 畢竟一開始也不會遇到什麼 corutine, async 的問題, 而且也不需要一開始寫就要一口氣搞懂 lifetime, trait, generic 等沒接觸過的概念, 也就不會上 stackoverflow 找答案 trail and error 就為了讓程式碼能跑, 讓我可以心神安寧的寫程式。

## RFC 1459 & homework spec
幾個我覺得很酷的地方:
1. 語法這種東西居然有 [psuudo code](https://www.freecodecamp.org/news/what-are-bnf-and-ebnf/) 可以表示, 而且還不錯用, 後續如果要拿來寫 cli, 這個東東會非常非常好用。
2. RFC 還是看得很不習慣, 很多東西都要細找, 重新看, 繞了不少圈
3. Wireshark 我的神, 之前一直懶得裝, 後來受不了去撈包下來看, 至少不會一直想東想西了
8. IRC 的規範不知道是我不適應 RFC, 還是很久沒更新了, 我到最後都沒搞清楚要怎麼知道哪些回覆要用哪種 prefix, 無可奈何之下只好用撈封包的方式來參考別人的作法。


## Rust 
1. 眾所皆知, rust 沒有完整的 OOP 支援, 我猜要寫可能還是可以自己實做一些功能出來, 畢竟有沒有支援 OOP 語法和能不能寫 OOP 完全是[兩回事](https://hackmd.io/@sysprog/c-oop)。 一路用 [Composition](https://www.youtube.com/watch?v=hxGOiiR9ZKg&ab_channel=CodeAesthetic) 的方式寫下來, 倒也沒有什麼不習慣的地方, 讓我蠻驚訝的, 不如說逃離 python 的魔掌我自己其實還挺爽的
2. rust 的生態系寫起來是真的舒服, `cargo watch` 等插件讓寫程式過程絲滑無比
3. rust 的 module 用起來比 go 的舒服太多了, 個人偏見
4. 之前看過一個建議新手寫 rust 的[方法](https://youtu.be/2hXNd6x9sZs?si=POoo3vx7464yVOGH&t=555)就是, 全部將用最沒效率但是最好寫的方法來做, 例如一言不合就把 String Clone 或是 Struct 直接 Copy, 所有的 string literal 想都不想就直接放進 heap (String) 等, 這個建議不得不說很適合只有用過動態型態或是有 GC 的語言的工程師, 一來是寫起來程式不會有那麼多錯, 不會一堆關於 reference 和 lifetime 的問題, 降低超級多學習曲線, 而且也可以讓自己認知到之前的程式語言或是寫法的有多 ~~蠢~~ 差, 而且如果你 GC 癮犯了, 也可以去玩玩看 rust 提供的 [reference counter](https://doc.rust-lang.org/book/ch15-04-rc.html)。
5. rust 的測試寫起來很舒服, 有種本來就鼓勵你寫測試的感覺(雖然不知道到底有沒有效)
6. rust 的 compilter 是我大哥, 至少現在是