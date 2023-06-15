---
title: 為什麼你該試試看 Enum
author: 春雨
categories: 未分類
date: 2023-05-31 17:26:59
tags:
---


## 為什麼你該試試看 Enum

一個幾乎每個工程師都會遇過的問題:

```python
weekday_list = ["monday", "tuesday", "wednesday", "thursday",  "friday"]

def is_weekday(weekday: str) -> bool:
  return weekday in weekday_list


is_weekday("123") # False
is_weekday("mondy") # False, but no error
is_weekday("monday") # True
```

這個寫法可以讓我們透過 `in` 關鍵字來檢查我們輸入的字串是否屬於 `weekday`, 但沒有辦法解決其他幾個問題:

1. 如果輸入是錯的, 那麼 `in` 會認為輸入值不屬於 `weekday`, 而無法幫助我們 raise error.
2. 我們無法強制要求 User 輸入範圍內的值


這時候 Enum 超人就出現了:

```python
from enum import Enum

class Weekday(Enum):
    Monday = "monday"
    Tuesday = "tuesday"
    Wendnesday = "wendsday"
    Thursday = "thursday"
    Friday = "friday"


def is_weekday(weekday: Weekday) -> bool:
    return isinstance(weekday, Weekday)

is_weekday(Weekday.Monday) # True
is_weekday("123") # False
```

由於 Enum 的 attribute 是 Enum 的 instance, 所以我們可以透過 `isinstance` 來檢查輸入值的格式是否為我們的 `Weekday` 範圍內