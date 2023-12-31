---
title: oauth2-implementation-line-example
author: 春雨
categories: 未分類
date: 2023-12-31 15:12:32
tags:
---

# 串接 oauth2 和 jwt, 以 line 為例

先上圖:

![](https://pbs.twimg.com/media/F_MrKxwasAAc7Vw?format=jpg&name=large)


> ref: https://twitter.com/bytebytego/status/1725770574402928644

## Authorization Code vs Implicit Code
### Authorization Code
前端和 authorize server 先取得授權, 取得 authorize code, 接著前端將 authorize code 送至後端, 後端不直接使用, 而是將 authorize code 送向後端, 後端接著將 authrozie code 向 authorzie server 驗證, 如果驗證通過, 則會回傳一個 access token, 後端再將這個 access token 向後端請求資料, line login api 的作法就是這種, 不過由於前端沒有實際上取得 access token, 如果後端的 access token 過期, 就請前端再登入一次, 後端只儲存 token 以及在 authorize 時拿去 auththorize server 驗證,
之所以這個流程會比後面提到的 Implicit code 更安全一點是因為:

1. 它還能再套一層 [PKCE](https://oauth.net/2/pkce/)
2. access token 由後端管理, 減少使用者被駭(安裝不安全的插件, 被挾持)導致 token 洩漏

### Implicit Code

一個比較不那麼安全的作法, 也是 line liff 的登入方式, 在前端向 authorize server 授權後, 直接由前端保管 access token, 每次請求都會附上一個 access token, 後端再驗證這個 access token 是否為 authorize server 所發放的, 缺點就是 access token 由前端管理

## JWT validation

在我們取得 access token, 以 line 為例, JWT 時, 一個最簡單的作法就是直接請 authorize server [驗證](https://developers.line.biz/en/reference/line-login/#verify-access-token)我們的 token, 不過每次都要問一次 line, 難免有效能上的問題, 這裡我們可以利用非對稱加密(ES256)的特性來做到半離線的驗證法, 如下圖

![](https://i.imgur.com/UnAI8cS.png)

ref: https://blog.miniasp.com/post/2023/04/09/How-to-validate-LINE-Login-issued-ES256-ID-Token
