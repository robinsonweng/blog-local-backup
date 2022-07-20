---
title: 如何將Json存入資料庫當中
author: 春雨
date: 2019-09-19 18:06:30
tags: [SQL, Database]
categories: [程式, DB]
---

## 有兩種解決的方法 ##

1. 在資料庫支援的情況下, 以Documention為輔完成任務
2. 沒有資料庫不支援的情況下, 將Json的每個key拆開變成獨立的表格, 並加上ID建立所謂的關係來連結不同的表格


```json, data.json

{
    "id": "1",
    "name": "Jane",
    "hobby":"Cooking",
    "Age": "10",
    "Pet": {
        "name" :"David",
        "species": "Dog",
        "age": "4"
    }
},
{
    "id": "2",
    "name": "Lucy",
    "hobby":"Jogging",
    "Age": "18",
    "Pet": {
        "name": "Kitto",
        "species": "Cat",
        "age": "4"
    }
}

```

> Table example:

`table: Name`

ID | Name | Hobby   | Age 
---|---|:---|:-:
 1 | Jane | Cooking | 10 
 2 | Lucy | Jogging | 18 

`table: Pet`

| ID | Name  | species | Age |
| - | - | :-: | - |
| 1 | David | Dog | 4 | 
| 2 | Kitto | Cat | 4 |