---
title: 截至現在為止, 我對記憶體的了解
author: 春雨
categories: ["Operating System"]
date: 2023-10-07 16:09:14
tags: [memory, c, rust]
---

整理一些零碎的知識, 範例大多為 c, 可能會有 rust or python

## stack & heap

## array
c 的 array 其記憶體位址為開頭元素的記憶體位置, 但因為 c 語言沒有邊界檢查, 所以 array 如果處理不妥當, 有可能會覆蓋到其他的記憶體空間
並且只要了解記憶體的大小, 可以直接對 pointer 加減來做 array index & traversal

## boundary check
檢查是否有程式碼存取超出範圍的內容, 其中一個 c 語言產生資安疑慮的問題


## struct 
和 array 類似, struct 成員的記憶體位置等同於 struct 的記憶體位置, 更有趣的地方在於, c 會幫你對齊成員的記憶體位址, 根據順序, 如果兩個成員的記憶體不超過8個 byte(根據作業系統 & 硬體架構會有所浮動), 則兩者會在同一段記憶體上, 如果超過則後面則會補滿至 8 byte 讓下個成員的資料結構能對齊

後續補上範例程式
## union
在 c 語言當中, union 的成員都共用同一個記憶體空間, 例如在同一個記憶體空間中的 float32, 可以透過 union 來根據情況存取要取整數或是浮點數
後續補上範例程式

## virtual memory
作業系統的 '記憶體支票', 