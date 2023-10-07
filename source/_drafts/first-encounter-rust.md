---
title: 第一次接觸 rust 
author: 春雨
categories: rust
draft: true
date: 2023-10-03 17:26:52
tags: ['rust']
---


其實嚴格上來講也不是第一次接觸了, 只不過因為不習慣靜態語言以及其 rust 的語法, 所以導致現在才正式開始寫, 以下統整幾個 rust 的特點:

## 沒有 gc
以往程式語言在做記憶體管控的時候分成兩個部份, 分別是 stack 和 heap, 前者是有規律的取用記憶體, 後者則是不規律的取用記憶體, 以往都只有兩條路, 依靠 SWE 的[素養](https://marek.vavrusa.com/memory/)來進行分配和施放, 或者是像 python 或是 go 那樣, 在一開始就設計好如何回收在 heap 的物件, 讓使用者(近乎)毫無顧慮的使用。 在 rust 這裡就有點不太一樣, 它希望透過一系列的規則來在 compile time 就能讓 SWE 決定物件的存活週期, 也就是 ownership 和 lifetime。這個強大的規則也是其中一個賣點之一, 讓使用者享受被抽象化的記憶體管理界面的同時, 又不需要去承受垃圾回收的[overhead](https://discord.com/blog/why-discord-is-switching-from-go-to-rust), 不過有些人會認為垃圾回收期間造成的『時間暫停』在大多開發情況下根本就微不足道

## 強大的社群生態
作為一個長期寫 python 的人來說, 沒有 package & environment manager 根本就是人間地獄, rust 的 cargo 幫我們從安裝套件, 管理專案, 環境設定, 甚至還可以[安裝插件](https://crates.io/crates/cargo-watch)來輔助開發

## oop?

## trait vs interface

先上例子 1:
```rust
struct User {
    nickname: String,
    realname: String, 
}

impl Eq for User {
    fn eq(&self, other: &Self) -> bool {
        self.nickname == other.nickname ||
        self.realname == other.realname
    }
}

fn main() {
    let user1 = User {
        nickname: String("Andy"),
        realname: String("Andy the bro"),
    };

    let user2 = User {
        nickname: String("Tony"),
        realname: String("Anthony"),
    };

    println!("Is user1 & user2 the same? {}", user1 == user2);
}

這種用法讓我想到 python 的 dunder method 像是 `__eq__`


第二種用法
```rust
trait Move {}

trait Bark {}

trait Engine {}

fn animal_move()
where
    Move + Bark
{}

fn car_move()
where
    Move + Engine
{}

```

mixin: interface with implmented function body
interface: interface with only function signature
trait: Both is acceptable



First the is-a relationship doesn’t always make sense: a dog might be a kind of mammal, a robot is not, but they both have move() operations. Prior to recent versions of Java that allowed default implementations in interfaces, you ended up copy and pasting code in this case.
reddit.com/r/rust/comments/cn20vu/traits_vs_interfaces/



```



## 跨語言支援

## unsafe rust

## 邊界檢查
c vs rust

## 誰在用 rust?
linux, discord, influxdb, turbopack, orjson

