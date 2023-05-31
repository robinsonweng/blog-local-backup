---
layout: posts
title: RESTful Service Best practice 2013
tags:
  - RESTful
  - http
author: 春雨
categories:
  - Web
  - 規範
date: 2022-01-08 13:08:47
---


# RESTful Service Best Practices (2013)


https://www.redhat.com/en/topics/api/what-is-a-rest-api
https://www.redhat.com/en/topics/integration/whats-the-difference-between-soap-rest
https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm
https://zh.wikipedia.org/zh-tw/%E8%A1%A8%E7%8E%B0%E5%B1%82%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2

https://restfulapi.net/

https://ihower.tw/blog/archives/1542

https://www.restapitutorial.com/

[原文連接](https://raw.githubusercontent.com/tfredrich/RestApiTutorial.com/master/media/RESTful%20Best%20Practices-v1_2.pdf)


## Overview
Restful 的六大規範為
- Uniform Interface
- Stateless
- Cacheable
- Client-Server
- Layered System
- Code on Demand

目前著重在前三點上面

## Http Methods
主要與 restful 相關的 http method有

- GET
- POST
- PUT
- DELETE

:::info
沒有直接相關的 method:
- OPTION
- HEAD
:::

## Uniform Interface
統一界面規範了使用者與伺服器之間的溝通界面. 它簡化以及減少了系統架構的耦合問題, 令每個部份都能夠各自獨立運行. 以下四個規範的統一界面為
- identifying resources
- manipulation through representations
- self describing messages
- HATEOAS
    - 一種能與使用者互動的規範

    :::info
    取自維基百科:
    ```json= 
    // 請求
    GET /accounts/12345 HTTP/1.1
    Host: bank.example.com
    ```

    ```json= 
    // 回應
    HTTP/1.1 200 OK
    {
        "account": {
            "account_number": 12345,
            "balance": {
                "currency": "usd",
                "value": 100.00
            },
            "links": {
                "deposits": "/accounts/12345/deposits",
                "withdrawals": "/accounts/12345/withdrawals",
                "transfers": "/accounts/12345/transfers",
                "close-requests": "/accounts/12345/close-requests"
            }
        }
    }
    ```
    :::

### Resource Based
每個資源透過uri去做辨認, 但資源本身與資源表示在概念上是分開的, 例如我們不會回傳整個資料庫, 卻會回傳類似概念的 json
### Manipulation of Resources Through Representations
當用戶端取得了資源表示方式, 包含任何附加的metadata, 它(表示方式)都應該要有足夠的資訊去修改或刪除伺服器上的資源, 前提是使用者有這個權限
### Self-descriptive Messages
每個情求都需要包含足夠的訊息來解釋如何消化這則訊息, 舉例: 哪個媒體該用哪種解析器(mime.type) 回應也會顯性的標示他們是否可快取

### HATEOAS
很像互動式的文檔, 假設使用者請求了某段網址的集合, 回應其集合網址

## Stateless
無狀態, 意指所有請求都應該是獨立的操作, 一個請求內就應該包含了所有的資訊, 身份認證, 參數, header, body, 當下請求狀態, 再交給伺服器去辨認
在 REST 規範下, 使用者應該要負責提供所有的資訊去滿足這個需求, 伺服器不需要去維護使用者的 session, 如果使用者需要多個回應那就需要送出多個請求(伺服器不會主動聯繫使用者), 由於我們不需要去維護使用者的狀態, 提供了伺服器很大的擴展性. 並且 load balancers 也免去了考慮 session affinity 的問題
有時候點開上一頁, 網頁提醒你說這個頁面不會儲存你的狀態, 其中一個原因就是它會違反stateless的規範. 
但也是會一些例外會打破這個規則, 如 three-legged OAuth, api call rate limiting, etc. 但請進可能達到這個目的

## Cacheable
顯性或隱性的定義回應是否能夠快取


## REST Quick Tips(速記重點)

### 透過 HTTP 動詞來進行動作
任何 API 接收者都有能力傳送, GET, POST, PUT, and DELETE 這些動詞, 他們很大程度上釐清了 API 當下的動作為何. 並且 GET request **不能** 更改任何資源. 測量或是追蹤可以更新資料, 但不能是 uri 能夠指定的資料

### 敏感的資源名稱
使用敏感的資源名稱或路徑
e.g.
```
// type 以及 id 都是非必要的, 這樣違反 restful api 的規範
/api?type=posts&id=23
```

```
// 這樣 type 以及 id 在網址中就是必要的, 符合 restful api 的規範
/api/posts/23
```

### XML and JSON

### Create Fine Grained Resources
先從細小的, 容易定義的 CRUD API 開始

### Consider Connectedness
連貫性, 如果分頁類的功能有 first, last, next, and prev 連接,會很有幫助

## 定義
### Idempotence
連續執行同一串網址, 結果(資料)是否有改變



| http method | is  Idempotence| 備註      |
| --------    | --------       | -------- |
| GET         | True           | Text     |
| OPTIONS     | True           | Text     |
| HEAD        | True           | Text     |
| TRACE       | True           | Text     |
| PUT         | True           | 有例外    |
| DELETE      | True           | 有例外    |
| POST        | False          | Text     |

<!--
post put delete state
-->


### Safety
:::info
這裡的 safe 指的是 thread safe 中的 safe
:::

以下 http method 不能改變伺服器的狀態
- HEAD
- GET
- OPTIONS
- TRACE

## Http 動詞
### GET
在 GET 用於讀取伺服器資料庫且不更改的狀況下, 他是 safe 的. 不要透過 GET method 去更改任何伺服器資源

### PUT
PUT 大多用於更新資料, PUTing to a known resource URI.
但 PUT 也可以用於創建資源, 假設使用者有決定好的資源 id 以及路徑, 就會使用 PUT 而不是 POST


### POST
Post 最常用的情境就是創建一個新的資源. 特別是用在從屬資源上, 也就是說, 創建一個新的資源, Post 到這個父類別然後伺服器負責關聯一個新資源在這個父類別之下, 並隨之分配一個 ID(新資源uri), etc.

```

// 創建一個資源 uri, customers/12345
POST http://www.example.com/customers

// 創建一個資源 uri, customers/12345/orders/45678
POST http://www.example.com/customers/12345/orders

```

POST 既不安全也不 idempotent. 它就是設計用來做 non-idempotent 的資源請求.
使用者請求兩個相同資訊的 POST request的話, 大多數情況是回傳相同的資源

### PUT vs POST for Creation
總而言之, 請優先選擇 POST 作為資源的創建. 除此之外, 如果使用者知道更新資源的完整 URI, 用 PUT; 如果使用者選擇創建新的資源, 或者在創建資源前不知道該資源的 URI, 使用 POST
### DELETE
DELETE是用於刪除該資源的 method

```
DELETE /customers/12345
DELETE /customers/12345/orders
DELETE
```
當 DELETE 成功時, 它回會回傳一個 200(ok) 和一個 response body. 他有可能是當下被刪除資源的資訊或是表示法(大多會消耗很多頻寬資源), 或一個包裝好的回覆(見下方表格). 或者回傳 204 No Content 並完全沒有 response body 會是比較推薦的回覆

在 HTTP 的規格上來說, DELETE 是 idempotent. 如果你 DELETE 一個資源, 它被刪除, 你重複呼叫 DELETE 只會得到相同的結果, 它被刪除. 如果將 DELETE 用於 decrements counter 的話, 那這個 DELETE 就不再是 idempotent. 就像之前提到的那樣, 用量統計和測量可能已經更新, 但如果服務是 non-idempotent, 我們可能還需要考量資源/資料是否有更改. 總而言之, 推薦在non-idempotent 資源的請求 使用 POST

### 總結



| HTTP Verb | /customers | /customers/{id} |
| --------  | --------   | -------- |
| GET       | 200(ok) list of customers. Use pagination if needed | 200(ok) single customer     |
| PUT       | 404 Not Found, 除非你想要更新整段資源       | 200 or 204. 404 if id not found. |
| POST      | 201, 'Location' header with link to /customers/{id} containing new ID       | 404     |
| DELETE    | 404, 除非你想要刪掉整段資源       | 202. 404 if id not found     |

## Resource Naming
為了更有效的利用 http method, 創建一個資源命名是一個易懂, 好操作的 API 是最重要也最具討論性的概念. 好的命名會讓 API 有很直觀的操作方式. 
在決定要為資源命名時, 不要使用動詞或行為而使用名詞去命名他們. 也就是說, 一個 RESTful URI 應該要代表一個資源為一個物體而不是一個行為. 名詞有特質而動詞沒有, 如:
- 系統內的使用者
- 學生註冊的課程
- 使用者的貼文動態
- 使用者的追蹤清單
- 一篇關於騎馬的文章

在一套服務裡面, 每個資源至少都要有一個 URI 去做辨認. URI 最好合理並能夠表達資源. URI 最好有:
- 可預測性
- 階層式結構

來實現可讀性以及可用性, 也就是說如果資料之間是有連續, 階層式的結構關係, 能夠幫助我們規範好的 RESTful API
### Use Case
插入一個新的 customer:
```POST /customers```

透過 customer id 去讀取一個 customer:
```GET /customers/33245```
相同的 URI 也可以用在 PUT 和 DELETE

創建一個新的 products:
```POST /products```

讀取/更新/刪除 products id 66432
```GET|PUT|DELETE products/66432```

如果 customer 要創建一個新的訂單的話呢?
一個可能的選項為:
```POST /orders```
這樣或許能創建訂單, 但 訂單與 customer 的關係被排除在了這段 URI 當中

一個比較清楚的 URI 為:

```POST /customers/33245/orders```
這樣不論是使用者或是伺服器都能很清楚的知道我們要為 customers #33245 創建一個訂單

那麼這段 URI 應該要得到什麼回應？
``` GET /customers/33245/orders```
有可能是 customers 創建的所有訂單
:::info
最好不要在一組集合(如上述)實現 PUT 或是 DELETE 的功能
:::

如果檢視所有訂單並不需要 customer 的過濾, 也可以直接發出這樣的請求:

```GET /orders/8769```


### Resource Naming Anti Patterns

#### Query in RESTful API
一個剛接觸 RESTful API 的使用者最常搞混的概念:
```
GET /servies?op=update_customer&id=12345&format=json
```
vs
```
GET /servies/customer/12345?format=json
```
差在哪邊? 其實很簡單, 因為 http query 在 URI 裡面不是必要的, 這樣並沒有很清楚的表達我們請求這個資源到底 **必須** 知道傳送哪些資訊.
簡單來說, 前者是 optional, 後者是 requirement.

#### Verb in uri
另外一個常見的錯誤是下面這個 uri
```
GET /update_customer/12345
```
以及他的邪惡雙胞胎
```
GET /customers/12345/update
```
你會在很多服務看見很多這類設計, 但最好的設計是透過 http method 表示 API 的動作, 在 URI 表示資源的名詞以及特徵, 這個反直覺的設計不但看起來痛苦, 甚至是危險的, 使用者分不清楚究竟決定伺服器行為到底是在 http method 還是在 uri 內, 例如以下 uri:

```
PUT /customers/12345/update
```
究竟更新 customers 12345 的因素是因為透過 PUT 這個 http method 呢? 還是 uri 當中的 update route? 這類設計是非常容易讓使用者搞混

### Pluralization
我們來談談關於名詞的複數以及個數, 請見以下 uri:



GET /**customer**/33245

vs

GET /**customers**/33245

兩種作法都有其合理的論點, 但比較能被人接受的作法是用複數來形容資源, 理由是 customers 是 customer id 的集合, 而 33445 則是表示 customers 的其中一個資源


### Wrapped Responses
服務有機會在同一個 request 回傳 header 和 body. 在許多的 js 框架當中, http status code 並沒有回傳到 end-dev 手上, 大多是為了防止 client 基於 status code 去中斷.
當下最標準的 json response 需要滿足以下特質
- code
    - http status code as an interger 
- status
    - 有三個型態, success, fail, or error. "fail" 用於 HTTP status 回傳值在 500-599之間, "error" 用於 400-499之間, 以及 "success" 是給剩餘的範圍 (1XX, 2XX, 3XX)
        :::info
        註: 在 http status code 的規範中(rfc 7231), 以上範圍的數值個別意義為:
        - 1XX:
            - Informational Responses
        - 2XX:
            - Success
        - 3XX:
            - Redirect
        - 4XX:
            - Client side error
        - 5XX:
            - Server side error

        參考資料: 
        [rfc7231](https://www.rfc-editor.org/rfc/rfc7231#section-6.5.1)
        [MDN 好讀版](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Status)
        :::
- message
- data

一個回傳成功的 json 會如同以下範例:
```jsonld=
{    
    "code":200,
    "status":"success",
    "data": {
        "lacksTOS":false,
        "invalidCredentials":false,
        "authToken":"4ee683baa2a3332c3c86026d"
    }
}
```

一個回傳失敗的 json 會如同以下範例:
```jsonld=
{
    "code":401,
    "status":"error",
    "message":"token is invalid",
    "data":"UnauthorizedException"
}
```


## Querying, Filtering and Pagination
以頻寬的角度來說, 將大量的資料切分為數個部份是很重要的, 這對於 UI 來說也很重要, 因為 UI 最多也只能顯示一小部份的資料. 在資料不停成長的情境之下, 預設回傳的資料量是ㄧ件相當關鍵的事情. 以向 Twitter API 請求使用者的動態為例, 如果沒有指定, 系統預設回傳 20 篇貼文, 即便在有指定的前提之下最大依然只能回傳 200 篇.
除了限制回傳的數量以外, 我們也需要考慮要如何 分頁/滾動 整組資料集, 回傳部份資料集並能夠 
在大量的資料集當中 "上一頁", "下一頁".
目前主要有兩組方法來限制分頁的查詢.
1. 索引的格式要嘛是 page-oriented 或是 item-oriented, 指定一個 id 或是一頁物物品
2. 指定一個範圍

也就是說 "以每 20 個物體為一頁, 給我第五頁" 或是 "給我第 100 個物品到第 120 個物品"
推薦伺服器上兩者都能實現, 有些 js 工具甚至使用 http header 中的 `Range` 作為分頁的索引

:::info
參考:
[MDN Headers: Range](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Range)
:::

### Limiting Results
就像上面提到的那樣, 我們希望伺服器最好能夠實現 query 的分頁法(offset 和 limit), 以及 http header 的 `Range` 方法. 要注意的是 query 以及 header 分頁都在同一個請求的前提之下, query 分頁法要能夠覆蓋 header 分頁法.

要實做兩個有時無法取得相同資料的功能在同一個功能裡面會不會令人很混淆? 我們其實是希望 query 分頁是給一般使用者使用, 而 `Range` 分頁是讓基於 http 規範的函式庫來操作

#### Limiting via the Range Header
使用者端透過 header `Range` 請求分頁的格式為:

```
Range: items=0-24
```

## Supporting CORS
### Handling Cross-Domain Issues

## Service Versioning
### 什麼時候要開始一個新的 API 版本?
- 更改一個特徵的名稱, 如: 從 "name" -> "firstName"
- 移除一個特徵
- 改變特徵的資料型態, 如: 從 int -> str, str -> bool
- 驗證的方式改變
- In Atom style link
- 一個必要的新資源被新增在了原本的工作流程內
- 資源概念改變; 資源的概念或意義或資源的狀態與以前相比有差異, 如
    - 舊的 content-type: text/html 原本會回傳一組連接, 新的變成會回傳一個 web browser form 作為使用者輸入
- 一個 API populating "endTime" ``` /users/{id}/exams/{id}``` 原本是學生當下註冊考試的時間, 現在的意思為排定的考試結束時間
- 新增一個新的區域

### 一些不會弄壞 API 的更新
- 新的特徵被加入到 json
- 新的連接被加入到其他資源中
- 支援新的 content-type 類型
- 新的 content-language 格式
### Deprecated
如果版本過期, 在 header 加入 `Deprecated: True`

## Date/Time Handling
時間戳記需要被服務以 UTC 或是 GMT 格式的方式儲存, 消化, 快取 etc
### 在 Body Content 的時間序列
有一個最簡單&通用的方式來解決這個問題————永遠都用一樣的格式, 包含時間的片段也一樣. ISO 8601 時間點格式是一個好的解決方法(yyyy-MM-dd'T'HH:mm:ss.SSS'Z'). 推薦所有的 request & response 以及 body content 都使用此標準

如果你剛好在開發 Java 相關的系統, `DateAdapterJ` 函式庫可以幫助你輕鬆解析和創建 ISO 8601 規格的日期和時間系統和 HTTP 1.1 (RFC 1123)格式的 header

至於用於基於瀏覽器的 UI, ECMAScript 5 規格包含如何用 JavaScript 解析和創建 ISO 8601 格式的日期. 這個規格是可以符合大多主流瀏覽器的. 
### 在 Headers 的時間序列

## 安全類服務
所有驗證類服務皆需要透過 SSL 傳輸, 包含 JWT, Basic auth, OAuth2 等
:::info
註: 安全類的內容有點過期, 如果要最新的驗證/授權機制請至 [OAuth2](https://oauth.net/2/)
:::
### Authentication(驗證)
當下最好的作法是透過 OAuth 作為驗證機制

### Authorization(授權)
服務的授權並沒有與驗證有很大的差別, 他的問題是基於: "這個規範有權限取得這個資源嗎?" 基於規格, 資源 和權限的三重奏, 我們是能夠有一個授權服務來符合這個概念. 我們可以透過這些抽象化
的概念來為每個授權規範建構一個可以快取的通行控制清單(ACL)

### Application Secutity
開發一個安全的網頁應用同樣適用於 RESTful 服務
- 在伺服器端驗證所有使用者輸入. 接受 "已知的" 好輸入, 拒絕壞輸入
- 能防止 SQL 和 NoSQL 注入攻擊
- 用已知的第三方函式庫來編解碼資料, 例如微軟的 Anti-XSS 或是 OWASP 的 AntiSammy
- 限制請求/回覆區域的訊息大小
- 服務應該要只顯示泛化的錯誤訊息(不要回傳詳細的程式錯誤訊息)
- 考慮商業邏輯攻擊法, 例如攻擊者可以跳過多重驗證的過程並不需要輸入信用卡資訊來送出購買請求嗎？
- 紀錄可疑的動作

RESTful 額外的安全考量:
- 驗證畸形的 JSON 資料
- 動詞應該要只能夠被使用在允許的 http method, 例如:
    - GET request 不能刪除任何實體. DELETE 才能
    - 要注意 race conditions

API Gateways 可以用在 API 的監控, 節流, 和通行控制上. 以下功能可以透過 Gateway 或是 RESTful 服務實現
- 監控 API 的使用量和檢查有沒有異常的流量
- 節流 API 用量來限制可疑的使用者透過 API 來進行 DDOS 和黑名單可疑 IP
- 儲存 API 金鑰在加密過的金鑰庫

## 快取和系統延展性
快取透過在系統增加一層來消除遠端呼叫取得的資料來給予系統延展性. 服務透過設定 headers 來賦予 response 能夠快取的能力. 在 HTTP 1.0 與 HTTP 1.1 的快取 header 是不一樣的, 所以伺服器需要支援兩者. 以下表格是 GET request 能夠支援快取的最少 header, 以及一些解釋和合適的值



| HTTP Header | Description | Example  |
| --------    | --------    | -------- |
|Date | Date and time the response was returned (in RFC1123 format). | Date: Sun, 06 Nov 1994 08:49:37 GMT|
|Cache-Control | The maximum number of seconds (max age) a response can be cached. However, if caching is not supported for the response, then no-cache is the value. | Cache-Control: 360 Cache-Control: no-cache |
| Expires | If max age is given, contains the timestamp (in RFC1123 format) for when the response expires, which is the value of Date (e.g. now) plus max age. If caching is not supported for the response, this header is not present. | Expires: Sun, 06 Nov 1994 08:49:37 GMT |
| Pragma | When Cache-Control is 'no-cache' this header is also set to 'no-cache'. Otherwise, it is not present. | Pragma: no-cache |
| Last-Modified | The timestamp that the resource itself was modified last (in RFC1123 format). | Last-Modified: Sun, 06 Nov 1994 08:49:37 GMT|

一個簡化的版本是
```
Cache-Control: 86400
Date: Wed, 29 Feb 2012 23:01:10 GMT
Last-Modified: Mon, 28 Feb 2011 13:10:14 GMT
Expires: Thu, 01 Mar 2012 23:01:10 GMT
```
以下是如果快取被關閉的情況下
```
Cache-Control: no-cache
Pragma: no-cache
```
### Etag Header
https://zh.wikipedia.org/zh-tw/HTTP_ETag
由於使用 Etag 可能會觸法, 我就沒有翻譯了, 取自維基百科:
> 由於HTTP cookie被越來越多的注重隱私保護的用戶刪除，可以將ETag用來追蹤唯一用戶。2011年7月，Ashkan Soltani和加州大學伯克利分校的一組研究者，包括Hulu.com將ETag用於追蹤用途。Hulu和KISSmetrics在2011年7月29日停止了這樣的追蹤，而KISSmetrics和超過20位其用戶面臨關於「無法刪除」的cookie的集體訴訟，其中包括了ETag的使用。 因為ETag由瀏覽器保存，並且在訪問統一資源時隨之後請求返回，一個追蹤伺服器可以輕鬆地重複設定任何從瀏覽器收到的ETag，以保持ETag不變，類似於持久cookie。額外的緩存頭部也能增強ETag的持久性。根據實現，ETag可以通過清除瀏覽器緩存清除。 