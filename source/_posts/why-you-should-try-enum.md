---
title: 為什麼你該試試看 Enum
author: 春雨
categories: [Python]
date: 2023-05-31 17:26:59
tags: [Python]
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
3. 即便 User 有輸入正確值的意願, 無法完全排除打錯字的可能
4. 有一堆 string literal 要維護


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
```

由於 Enum 的 attribute 是 Enum 的 instance, 所以我們可以透過 `isinstance` 來檢查輸入值的格式是否為我們的 `Weekday` 成員內。 而且因為我們的選項被限制住了, 所以不能輸入這些選項以外的值(問題 2), 由於我們是透過 Enum 的成員來選擇值, 也不需要維護一堆零散的字串(問題 4, 問題 3), 而剩下 問題 1, 我們可以透過實做 dunder method 賦予 Enum 這個特性


```python
from enum import EnumMeta
class EnumContains(EnumMeta):
    def __contains__(self: type, member: object) -> bool:
        try:
            self(member)
        except ValueError:
            return False
        else:
            return True

class EnumExample(Enum, metaclass=EnumContains):
    Monday = "monday"
    Tuesday = "tuesday"
    Wendnesday = "wendsday"
    Thursday = "thursday"
    Friday = "friday"


if "friday" in EnumExample:
    print("friday is a valid member of enum!")
```

很讚, 對吧。但還是得碎碎念一下, 這些資料型態的用法都是在 Python 以外的語言學到的, 總感覺哪邊不對...