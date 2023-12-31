---
title: CSRF 簡介
author: 春雨
categories: ['Security']
date: 2023-12-31 11:38:36
tags: CSRF
---


## What is CSRF, again?
假設一個網站的儲值的 URL 如下, 並且透過 cookie, session 等登入機制:

```htmlembedded 
<!-- website host: example.com -->
<form action="https://example.com/depositAdd" method="post">
    <label for="name">Name:</label>
    <input type="text" id="name" name="name"><br><br>
    <label for="amount">Amount:</label>
    <input type="text" id="amount" name="amount"><br><br>
    <input type="submit" value="Submit">
</form>
```

那麼, 在網站沒有指定 cookie 只能對他們指定的網域生效時, 駭客可以架設一個釣魚網站, 並嵌入類似的程式碼
```htmlembedded 
<!-- website host: exomple.com -->
<form action="https://example.com/depositAdd?amount=1000&name=Andrew" method="post">
</form>
```

由於伺服器沒有設定允許 host 網頁的 allow list, 所以不論是來自 `exmaple.com` 或者是來自 `exomple.com` 的請求(ajax, api)都會接受, 並且 cookie 沒有設定 `samesite`, 所以任何 host 都能使用在 `exmaple.com` 設定的 cookie

## Cookie protection from browser
我們先來清點一下瀏覽器提供對 cookie 的保護, 他們分別是:
- HttpOnly
- Secure
- SameSite

### httpOnly
無法被 javascript api `Document.cookie` 存取, 是僅限瀏覽器和 server 之間溝通的 cookie

### Secure
如果瀏覽器和伺服器之間溝通的協定不是 `https`, 那麼瀏覽器無法讓伺服器設定這個 cookie, 也不允許沒有 `https` 的伺服器來存取這個 cookie

### SameSite
SameSite 讓 server 決定 browser 要以什麼樣的政策和不同網域的伺服器分享 cookie. 一共有以下三個選項:
- Strict
- Lax
- None

#### Strict
僅允許同個 Origin 才能存取 cookie

#### Lax(default)
和 Strict 相似, 差別差在如果使用者從別的網站被導向到 origin, browser 同樣也會分享此 cookie, 例如外部連接, 除此之外 Lax 禁止了比較不安全的 POST

#### None
一律允許, 但是是在 `Secure` 開啟的前提之下

## 幾個防護手段
OWASP 對於 CSRF 有幾個點是值得提出來的, 以 django 的 csrf token 為例子, 他其實是一種 STP(Synchronizer Token Pattern), 以下取自[維基百科](https://en.wikipedia.org/wiki/Cross-site_request_forgery#Synchronizer_token_pattern)

> STP 是一個每次使用者呼叫一個請求, 網頁的應用程式就會產生一個 unique token 並嵌入在 html 裡面, 並且 token 會在 server 端驗證. Token 會用任何能確保隨機並確保無法被預測 & 唯一的方式進行生成(e.g. 使用 hash chain 或是隨機種子). 使攻擊者無法猜測這些 token 繞過這些驗證

以 django 的 csrf_token 為例子

```htmlembedded
<input type="hidden" name="csrfmiddlewaretoken" value="KbyUmhTLMpYj7CD2di7JKP1P3qmLlkPt" />

```

> 因為 STP 只依賴 html, 所以他有很好的相容性, 但會會在後端伺服器產生一些複雜性, 因為要逐個請求進行確認, 並且 token 是隨機且不可預測的, 並且也會在使用者使用上產生一些問題, 例如原本的訪問順序為頁面 1, 2, 3, 但當使用者使用回到上一頁之類的功能, 就有可能使 token 失去原本的同步

而 OWASP 對於 [STP](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#synchronizer-token-pattern) 有幾個建議的做法:

CSRF Token 的生成:
- CSRF Token 應該要在伺服器端產生
- CSRF token 可以自選每個 session 刷新或是每個請求產生
- 伺服器應該要比對來自使用者的 token 是否存在和合理
    - 請求內是否存在 token
    - 請求的 token 和 session 的 token 不符

CSRF tokens 應要有以下特性:
- Unique per user session
    - 並沒有限制一定要 per request
- Secret, 足夠隱密
- 無法被預測, 例如利用 [/dev/random or /dev/urandom](https://zh.wikipedia.org/zh-tw//dev/random)
- 攻擊者無法重新生成一個完全一樣的 token
- CSRF token 不應該透過 cookie 傳送
- GET request 不但使用了 CSRF 沒有比較安全, 還有可能會在多個地方洩漏, 例如瀏覽紀錄


以及還有一個小細節:

```htmlembedded
<form action="/transfer.do" method="post">
<input type="hidden" name="CSRFToken" value="OWY4NmQwODE4ODRjN2Q2NTlhMmZlYWEwYzU1YWQwMTVhM2JmNGYxYjJiMGI4MjJjZDE1ZDZMGYwMGEwOA==">
[...]
</form>
```

透過 javascript 將 token 設在客製化的 header 會比放在 form 裡面的隱藏欄位安全一點, 因為有客製化的 header 的請求會自動適用於同源政策


## Conclusion
如果要我寫一個 tl;dr, 那就是: 如果是 Server-Side-Render, 直接使用 session, 是最快最省事, 而且有一定的安全性的方案。前後端分離一樣可以用 session, 但因為 csrf token 不能在前端產出來, 變成後端還是要有一個登入頁面的前端, 而且 api 的 domain name 通常會有很多個 host(SameSite), 相較之下不但比較不安全, 執行也很麻煩


## 參考資料
https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Cookies
https://owasp.org/www-project-secure-headers/
