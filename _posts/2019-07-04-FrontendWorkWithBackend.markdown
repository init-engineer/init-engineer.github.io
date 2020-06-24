---
date            : 2019-07-04 18:00:00
layout          : post
title           : Vue 前端工程師與 Laravel 後端工程師的權責分配問題
subtitle        : 事實上 Laravel 本身也有定義好前端與後端這部分的權責界線，基本上來說，你到 Laravel 的官方文件當中，會有一個 MENU 是 Frontend 的說明文件 ...
description     : "事實上 Laravel 本身也有定義好前端與後端這部分的權責界線，基本上來說，你到 Laravel 的官方文件當中，會有一個 MENU 是 Frontend 的說明文件 ..."
image           : https://i.imgur.com/WAENCqV.png
optimized_image : https://i.imgur.com/WAENCqV.png
category        : tutorial
tags            :
- Laravel
- Vue
author          : kantai235
origin_url      : https://kantai235.github.io/FrontendWorkWithBackend
---

今天收到一則有趣的問題，問題大概是這樣子的：

> 我是住在 Japari Park 的 COBOL，剛從大學畢業，之前就是讀資工系，但對前端比較有興趣，所以就在網路上購買了線上教學的課程，現在找到工作了，找的工作是 Vue 前端工程師，而面試時都是回覆我寫 Vue 等等都沒問題，但開始上班後發現並不是很純粹的前後端分離，而是前端人員在 Laravel 5.3 的 PHP 框架下面寫 Vue，這讓我壓力很大，因為完全是不同生態系的東西，而且前人也沒有留下交接文件！想詢問，我不想逃避問題，而是從中去成長，但是我應該怎麼做呢？能給予一些建議或分享一些建構的文章嗎？我翻遍網路上的範例，照著做卻遇到很多問題，實在是ＱＱ

首先我們要釐清這個壓力很大的問題來源，從你的情境描述我們發現很有可能是對於權責的界線模糊不清，導致誤以為自己要負責、要學習的內容可能非常龐大，而且前人就算有留下交接文件，這份交接文件對你來說可能並沒有什麼實質幫助，事實上並沒有那麼複雜，單純分工不清楚而已，有許多種解決方案，以下我列舉各種情境：

如果是前後端分離，那基本上你不會碰到這些問題，但顯然今天這個解決方案是被 disable 的。

如果是要共同維護同一件專案，那對於 Laravel 後端工程師而言，他的工作內容可能就只是把 API 寫好，然後通過 PHPUnit 測試就沒他的事了，而對於一位前端工程師而言，其實只要管好 resources 下的 js、lang、sass、view，僅僅如此而已，你只需要會介接 API 或者 JWT(JSON Web Tokens) 即可。

> - JWT 參考資料 [JSON Web Tokens - jwt.io](https://jwt.io)
> - 科普 PHPUnit [PHPUnit – The PHP Testing Framework](https://phpunit.de)

事實上 Laravel 本身也有定義好前端與後端這部分的權責界線，基本上來說，你到 Laravel 的官方文件當中，會有一個 MENU 是 Frontend 的說明文件，基本上那部分可以非常確定是前端的權責，如果確定是要共同維護同一專案的話，那麼你確實需要瞭解一下其內容。

*  [Laravel - Blade Templates](https://laravel.com/docs/master/blade)
*  [Laravel - Localization](https://laravel.com/docs/master/localization)
*  [Laravel - Frontend Scaffolding](https://laravel.com/docs/master/frontend)
*  [Laravel - Compiling Assets](https://laravel.com/docs/master/mix)

如果今天除了是要共同維護同一專案，並且不是以 JWT、OAuth 之類的方式開 API，而是要使用 Laravel 的 Model 來抓取資料，那麼你可能要去了解一些基本的 PHP 寫法，以及 ORM 相關知識，以便你在資料上的取用。

> - Eloquent ORM 參考資料 [Eloquent: Getting Started - Laravel - The PHP Framework For Web Artisans](https://laravel.com/docs/master/eloquent)
> - 科普 OAuth [OAuth - Wikipedia](https://en.wikipedia.org/wiki/OAuth)

到這邊為止，基本上是作為一位前端工程師，如果要在共同維護同一專案的前提下，與 Laravel 後端工程師合作所需要了解的知識，乍看之下好像都是前端工程師要做什麼做什麼，而不是後端工程師該做什麼做什麼？但老實說我並不清楚你們後端工程師的程度到哪邊，但基本上後端工程師應該負責後端與資料庫，從 `Route` 定義基本的路由、`Middleware` 去做好權限、安全機制的管控，限定 `Request` 所該傳入的內容與格式，傳入資訊後透過 `Validation` 做內容審查，制定好 `Controller` 並且將商業邏輯寫在 `Service`，以及商業邏輯的觸發與監控 `Event&Listener`，並且將資料庫邏輯的 `Repository` 寫清楚，到這邊會牽涉到 `Eloquent ORM`，也就是要把 `Model` 物件、關聯給寫好，以及很多軟體工程、資訊安全 ... 等等，基本該注意的。

> - 後端基本上該注意的: [P1. 純靠北工程師怎麼做的？就只是個簡單的後端架構而已。 · 乾太 Kantai](https://kantai235.github.io/2019/02/14/P1HowDoYouRelyOnTheKaobeiEngineer/)

至於建議一些內容，如果你是說你想更了解如何更清楚的定義前端與後端的權責範圍，那你可以參考這兩本書，前者是在講 Laravel 的基本知識，後者是在講軟體開發架構。

- Matt Stauffer，2017/05/15，《Laravel 啟動與運行》，歐萊禮出版社
- 福勒，2010/04/01，《企業應用架構模式》，機械工業出版社