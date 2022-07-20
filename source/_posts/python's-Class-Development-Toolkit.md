---
title: Python's Class Development Toolkit 
author: 春雨
categories: [程式, Python, OOP]
date: 2019-10-06 14:47:33
tags: [Python, classtoolkit]
---

## Python's Class Development Toolkit 的重點整理

{% colorquote info %}
`Raymond Hettinger` 一位常常出現在Python程式語言演講的大佬，曾經聽過他的`Transforming Code into Beautiful, Idiomatic Python` 聽過一次就愛上了他的演講風格，乾淨、簡潔、速度適中且幽默十足
{% endcolorquote %}

1. `__init__` is not a constructor:
    - 我曾經在寫Python也有這個疑問，為甚麼Python的建構子是叫做`__init__`而不是`__constructor__`?而實際上確實不是，self是本身class的實作，也就是說在`__init__`宣告以前，class已經建構好了，他是一個`initializer`，任務是將影片中的Radius存入字典當中。
    2022-1-9 update:
    如果真的要說python的建構子，那可能是 __new__，他是真的在class建構以前就開始運行了，所以如果有段程式碼想要在 __init__ 以前運行，可以選擇 __new__

2. `@classmethod`:
    - 如果我想要只呼叫一個method就可以達成:計算新的Radius同時更新到Class裡面，你可以在method中直接呼叫Class本身，但是這個舉動會導致繼承以後的method被影響，所以為了呼叫原class更新參數的當下又不會影響到繼承後的class，就是該使用 `@classmethod`中的cls參數來呼叫class的場合

3. `@staticmethod`:
    - 我想要寫一個function在class裡面，但是這個function的功能與class無關，所以你會陷入一種
    ```
    1. 寫進class裡面，但是用這個與class無關的function還是要實作一次物件
    2. 寫在class外面，但是這樣使用這個class的人就會找不到這個function
    ```

    - 這時候就是`@staticmethod`的出場時刻了，不需要實作但是仍然放在class裡面的method

4. `__method`:
    - 當我們在一個class當中要從一個method呼叫另外一個method我們會用`self.method()`但是self其實是指實作後的自己，也就是說self不只是class他自己，也包括被繼承的那些class，當繼承父class的子class也使用了相同名稱的method，原本的父class method就會被覆蓋。那麼要怎麼解決這個方法呢? 講者這邊提出個兩種方法，第一種方法就是在method被呼叫以前先複製一份，這樣一來，父class有自己的一份，繼承的子class也不會蓋過去原class的method，皆大歡喜(? 但是這對你來說是好方法，對其他人來說也是好方法，如果子class也照做一次，這段程式碼又會失效了XD
    ```
    def method(self):
        ...something
    
    _method = method
    ```

    「所以為了贏得這個小學一年級的學你戰爭，我們需要在複製以前加上名字，這樣他只要複製到你很醜，他看到的就是某某很醜」，也就是在method前面加上class的名稱避免誤用，而這套流程因為太實用了，所以Python把它加了進去，也就是所謂的`__method`
    {% colorquote info %}
    所以其實`__method`跟privacy一點關係都沒有，反而是他的相反，它可以讓你的子class能夠更自由的去繼承、覆寫
    {% endcolorquote %}

5. `@property`:
    有兩個用途
        1. 是在取得`.屬性`的時候，可以神奇的執行一件事情並取得這個值
        2. 當為了執行效率使用`__slots__`時，他會破壞原本取得變數的字典，而`@property`正是他的補救方案

結語:
    這篇演講真的是非常非常的紮實，學到很多東西以及觀念補正，推薦正在看class的朋友們!

有沒有曾經看過`@classmethod`或是`@staticmethod`的語法但還是搞不清楚應該用在甚麼情境的? 那你應該也來聽聽看[這個演講](https://www.youtube.com/watch?v=HTLu2DFOdTg&feature=youtu.be)