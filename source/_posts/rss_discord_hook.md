---
title: 將社群軟體通知以RSS整合至Discord
author: 春雨
categories: [程式, 教學]
date: 2020-3-10 19:15:33
tags: [IFTTT, RSS, webhook, Discord]
---

曾經因為臉書演算法導致漏失掉重要訊息，於是我決定將臉書、推特、與P站所有的通知都整合進RSS reader，後來得知可以透過ifTTT將rss以webhook的方式在discord上面傳送通知，就變成了只要有RSS就可以掛在discord裡面，部落格、學校通知、繪師更新，只要有RSS，所有通知一覽無遺。

## 工具介紹

1. [RSSHUB](https://docs.rsshub.app/)

    RSShub能幫助你將網站更新內容轉換為RSS通知的神奇網站，如果你技術力足夠，也可以根據官方網站步驟申請自己想要的網站。其中包含:
    - Facebook
    - Twitter
    - Pixiv
    - Youtube 等網站

2. [ifTTT](https://ifttt.com/)
    
    這個網站主打一個很簡單的邏輯:
    
    {% colorquote info %}
    「如果這件事情發生了，那就做這件事情」
    {% endcolorquote %}
    
    以不需寫程式，接拼圖的方法來打造自動化王國，從「如果相機拍照就將照片上傳至GoogleDrive」，到「按一個按鈕就讓家中智慧家電開燈(網站有支援的前提)」應有盡有

3. Discord的webhook網址


## 使用流程

- 首先在 RSSHUB中找到你想要的網址，假設我想得到猫宮ひなた的推文通知、轉推但不需要回復，根据官網說明的路由:

![](https://i.imgur.com/2rOqoyX.png)

我的RSS網址應該長這樣:

{% colorquote success %}
https://rsshub.app/twitter/user/Nekomiya_Hinata/exclude_replies
{% endcolorquote %}

- 接下來到ifTTT網站，註冊/登入以後，點選右上角的explore

![](https://i.imgur.com/DWd08ZO.png)

- 點選那個很不顯眼的加號

![](https://i.imgur.com/QH0UOcl.png)

- 我們就來到先前提到的 「if this then that」，點下「This」

![](https://i.imgur.com/UuqBlSQ.png)

- 在搜尋框裡面輸入RSS

![](https://i.imgur.com/oGjcaBp.png)

- 點開RSS選擇左邊的觸發條件「每當有新通知時觸發」

![](https://i.imgur.com/kdjutFe.png)

- 輸入剛剛從RSShub拿到的網址後點選「Create Trigger」

![](https://i.imgur.com/0xHzaM0.png)

- 這步跟剛剛一樣點選 「That」，只是這次要搜尋「webhooks」

![](https://i.imgur.com/qwbZnrJ.png)

- 點選「Make a webrequest」以後，接下來就是重頭戲

![](https://i.imgur.com/qhm5Zlp.png)

1. URL: 這邊要輸入的是discord當中的webhook網址，可以參考[Discord官方教學](https://support.discordapp.com/hc/zh-tw/articles/228383668-%E4%BD%BF%E7%94%A8%E7%B6%B2%E7%B5%A1%E9%89%A4%E6%89%8B-Webhooks-)

2. Method: 由於我們是需要傳送訊息到Discord，所以這邊一律設定為Post

3. Content Type: 這邊選擇application/json

4. Body: 這裡就是我們要傳送至discord的文字，雖然他支持discord的embed message，不過這邊我們先簡單一點，標記一個人並傳送標題與網址

選擇下面的小按鈕「Add ingredient」
這邊可以調整你要rss通知中的內容出現在指定的位置

![](https://i.imgur.com/78dpqYD.png)

5. 最後按下action就皆大歡喜了

P.S 由於RSShub是採取贊助制度，速度不一定可以讓你滿意，可以去尋找其他服務代替