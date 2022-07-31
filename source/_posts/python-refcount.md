---
title: Python的垃圾回收
author: 春雨
categories: ['python']
date: 2022-07-31 13:15:49
tags: ['c', 'python', 'garbage collect']
---


# Python Garbage Collect

## 前言

當初在學Python的時候，那時還年輕，以為底層甚麼的都幫我寫好了，C甚麼的好麻煩。現在回頭了解Python的記憶體配置，GC，VM等全部都是C寫成的，後悔沒有把C學好，沒辦法直接深入Python原始碼內，這篇算是一種補償心態吧XDD

## Why GC?

在C語言當中，我們需要掌控所有使用者模式(user mode)的一切電腦資源，從系統呼叫，分配記憶體，執行序，到陣列長度，宣告常數類型等大大小小的細節。假設今天使用者沒有對應的計算機素養，很容易覺得厭煩、沒有意義、難於使用，寫出來的程式也很容易出現問題。但Python不一樣，他的目標是語法簡單明瞭、容易學會，所以自動回收電腦資源自然也是Python需要面對的議題。

## Why understand GC?

大部分時間，在寫python並不需要這些背後的知識，但，了解Python的GC就像瞭解linux寫出來的C一樣。同樣的語言，給不同素養的人寫出來的效率、可讀性，就會不一樣。


[Why use `join` instead `+` in Python ?](https://towardsdatascience.com/do-not-use-to-join-strings-in-python-f89908307273)

## Where is GC?

{% colorquote info %}
這篇牽扯到python的記憶體分配、python的傳值方法[Pass Object by Reference](https://robertheaton.com/2014/02/09/pythons-pass-by-object-reference-as-explained-by-philip-k-dick/)、everything in python is object、python直譯器以及其VM，如果對這些內容還不熟悉，可以先去補充一下基本知識
{% endcolorquote %}

## When you hit enter

當我們執行一段程式碼的時候，直譯器會把程式碼拆成兩部分，分別是block&變數名稱 以及 object

<!-- pic for block & object vs stack & heap-->

block和變數名稱會被放進直譯器裡面的stack，而object會被放進heap裡面。在寫C的時候，我們透過記憶體位址操作、呼叫資料結構，而Python為了避免使用者直接接觸到記憶體議題，將記憶體位址改為`id`

{% colorquote warning %}
雖然id(Identity)，語意為身分、身分識別，但在Python的[官方文檔](https://docs.python.org/3/library/functions.html#id)中提到:

> CPython implementation detail: This is the address of the object in memory.

所以`id`雖然不是平常表現記憶體位址的十六進位數字，但也不是隨隨便便的一串數字
{% endcolorquote %}

## Reference Count

要能夠回收不需要的資源，首先得先知道哪些資源正被使用，那些沒有被使用。算出當下物件被多少人使用，並將沒有人使用的物件(計數器為0)的物件回收，就是引用計數的目的。

<!-- pic for reference count -->
每當物件的引用增加(通常是宣告、繼承)，這個物件的計數器就會+1，相反，當這個物件的引用減少，例如呼叫`del`，那麼這個物件的計數器就會-1

### 優點

- 實作簡單
- 回收過程不需要有很長的停頓

### 缺點

- 需要額外的記憶體空間去存放計數器
- 在linux底下運作時，[計數器更新會頻繁觸發COW](https://instagram-engineering.com/dismissing-python-garbage-collection-at-instagram-4dca40b29172)

### Pass by object reference(以list為例)

在C語言中，假設要複製一個array的值

```c=
int a[9] = {0, 1, 2, 3, 4, 5, 6, 7, 8};
int *b;
b = a;

printf("the direction of b is at: %p\n", &b);
printf("the direction of a is at: %p\n", &a);
```

印出來的會是兩個完全不一樣的位址(pass by value)，又或者透過傳地址標的方式來達到 pass by reference
但在python中，假設我們對`list`做出相同的操作

```python=3.6
a = [1, 2, 3, 4]
b = a

print(f'the direction of b is at: {id(b)}\n')
print(f'the direction of a is at: {id(a)}\n')
```

我們會的到兩個完全相同的`id`(pass object by reference)

也就是為甚麼我們複製一個`list`需要import [`copy`](https://docs.python.org/3/library/copy.html)這個標準函式庫，才會真正複製一個新的list出來，因為在Python當中，`b=a`只不過是將`a`的`id`複製給`b`，也就是共享同一個`list`的引用而已

### 傳遞引用的所有權

從上個例子可以看到，當我們宣告某個變數時，他並不擁有這個物件，而是擁有這個物件的`id`，也就是物件的引用，藉此將記憶體分配、施放記憶體等議題從使用者的層面隔離掉。為此，當無人擁有這個物件的引用時，python得負起責任釋放掉這個物件

## Tracing

雖然引用計數可以幫助我們找出那些閒置的物件並加以回收，但這時候會遇到一個問題。
<!-- pic for circulus ref -->
<!-- spider man meme -->


假設我們今天有兩個物件互相引用呢?你引用我，我引用你，無論如何計數都是1。這就是循環引用(reference cycles)，這類現象常常會出現在遞迴、雙向連接串列。而這就是tracing的核心目的，偵測出循環引用並加以排除。python使用的是標記-掃描演算法(Mark & Sweep)

![](https://i.imgur.com/fNKBCap.jpg)

### Mark & Sweep

下圖是物件互相引用的例圖

![](https://i.imgur.com/rLd6nzb.png)

基於python的記憶體配置，所有的物件在生成前就被串成雙向環形串列，如下圖，又稱為arena

![](https://i.imgur.com/6AsoX7k.png)

#### 標記-清除演算法

首先，先將計數器複製到左方格子中

![](https://i.imgur.com/KQApw3e.png)

接下來，只要是被python物件引用的，我們就對剛剛複製的計數器-1，所以像obj1這種被root引用的，我們就不更動

![](https://i.imgur.com/ASIu9jr.png)

接下來，我們把這些串列分成兩組:

1. reachable
    - 經過-1後計數器不為0或者有被正在活動的物件引用
2. unreachable
    - 計數器為0而且沒有被正在活動的物件引用

![](https://i.imgur.com/zf2Ukeb.png)

最後，我們將unreachable整個串列釋放，並將reachable串列還給head，完成這次的GC

![](https://i.imgur.com/tCCOeI8.png)

### Generation Collection

這時候可能就有人要問了，假設今天我有很多瑣碎的小物件不斷的進行分配和釋放，是不是就會需要不斷的去進行這種走訪串列，頻繁的去修正引用記數呢? 答案是會的，為此，python還引進了分代回收，分類出高頻率以及低頻率的物件進行回收，而不至於要走訪所有的串列
