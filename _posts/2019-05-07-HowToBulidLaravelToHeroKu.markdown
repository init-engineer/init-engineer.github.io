---
date            : 2019-05-07 12:00:00
layout          : post
title           : 如何將你的 Laravel boilerplate 專案成功部署到 HeroKu
subtitle        : 事情是這樣的，最近在研究 HeroKu 這家雲端平台，想要嘗試將一些服務部署到 HeroKu 上頭，但卻遇到一些問題，因此將一系列的問題解決過程記錄下來 ...
description     : "事情是這樣的，最近在研究 HeroKu 這家雲端平台，想要嘗試將一些服務部署到 HeroKu 上頭，但卻遇到一些問題，因此將一系列的問題解決過程記錄下來 ..."
image           : https://i.imgur.com/1XvupoJ.png
optimized_image : https://i.imgur.com/1XvupoJ.png
category        : tutorial
tags            :
- Laravel
- HeroKu
author          : kantai235
origin_url      : https://kantai235.github.io/HowToBulidLaravelToHeroKu
---

## 研究背景

事情是這樣的，最近在研究 HeroKu 這家雲端平台，想要嘗試將一些服務部署到 HeroKu 上頭，但卻遇到一些問題，因此將一系列的問題解決過程記錄下來。

---

## HeroKu 是什麼？

> **Heroku**是一個支援多種 [程式語言](https://zh.wikipedia.org/wiki/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80) 的雲 [平台即服務](https://zh.wikipedia.org/wiki/%E5%B9%B3%E5%8F%B0%E5%8D%B3%E6%9C%8D%E5%8A%A1) 。在2010年被 [Salesforce.com](https://zh.wikipedia.org/wiki/Salesforce.com) 收購。Heroku作為最開始的 [雲平台](https://zh.wikipedia.org/wiki/%E4%BA%91%E7%AB%AF%E8%AE%A1%E7%AE%97) 之一[1]，從 [2007年6月](https://zh.wikipedia.org/wiki/2007%E5%B9%B46%E6%9C%88) 起開發，當時它僅支援 [Ruby](https://zh.wikipedia.org/wiki/Ruby) ，但後來增加了對 [Java](https://zh.wikipedia.org/wiki/Java) 、 [Node.js](https://zh.wikipedia.org/wiki/Node.js) 、 [Scala](https://zh.wikipedia.org/wiki/Scala) 、 [Clojure](https://zh.wikipedia.org/wiki/Clojure) 、 [Python](https://zh.wikipedia.org/wiki/Python) 以及（未記錄在正式檔案上） [PHP](https://zh.wikipedia.org/wiki/PHP) 和 [Perl](https://zh.wikipedia.org/wiki/Perl) 的支援。基礎作業系統是 [Debian](https://zh.wikipedia.org/wiki/Debian) ，在最新的堆疊則是基於Debian的 [Ubuntu](https://zh.wikipedia.org/wiki/Ubuntu) [2]。
> 
> 資料來源：[Heroku - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/Heroku)

簡單來說，它就是提供一個平台讓你架設專案的服務，你只需要一個帳號，你就可以將你的 Laravel、MariaDB、Redis 一起架設上去，而且完全不收費，只是機器會睡覺而已，是個相當不錯的服務。

---

## 我應該怎麼開始？

首先你會需要申請一個 HeroKu 的帳號，HeroKu 會需要你的 First name、Last name、Email address、Role、Country 這些基本資料。

> HeroKu 註冊網址：[Heroku | Sign up](https://signup.heroku.com/login)

![https://i.imgur.com/9Aeooh9.png](https://i.imgur.com/9Aeooh9.png)
HeroKu 註冊新會員

接下來我們會需要建立一個新的應用程式，然後把 Laravel、MariaDB、Redis 放到應用程式當中，將整個服務建置起來。

![https://i.imgur.com/MOJLAZs.png](https://i.imgur.com/MOJLAZs.png)
選擇「Create new app」

輸入你這應用程式的名稱，以及選擇伺服器所架設的地區。

![https://i.imgur.com/DiSJbtt.png](https://i.imgur.com/DiSJbtt.png)
設定 App 的名稱、位置

建立應用程式成功之後，你會看到你應用程式的儀表板，這裡記錄著所有的歷史紀錄，無論是錯誤訊息、設定更改或者建立新部署，現在假設我們需要部署新的 Laravel 環境上去 HeroKu 的話，我們先進入「Deploy」的地方。

![https://i.imgur.com/xDf1vZw.png](https://i.imgur.com/xDf1vZw.png)
應用程式的儀表板

你會看到 HeroKu 提供了多種部署方式，有 Heroku Git、GitHub、Container Registry 這三種方式，這次我們使用 GitHub 的方式來將專案部署上去。

![https://i.imgur.com/lE0C0nZ.png](https://i.imgur.com/lE0C0nZ.png)
HeroKu 提供多種部署方式

透過 GitHub 部署的方式很簡單，你只需要連接你的 GitHub，然後搜尋你要部署的專案，舉例來說我想部署 kantai235.github.io 這個部落格，我就直接搜尋「kantai235」然後 Search(搜尋)，就能找到我的部落格專案，接下來直接按下「Connect(連接)」就能將專案部署到平台當中了。

![https://i.imgur.com/X8xoLkb.png](https://i.imgur.com/X8xoLkb.png)
透過 GitHub 部署到 HeroKu 平台當中。

不過這時候如果你的 Laravel 專案是以 rrappasoft/laravel-5-boilerplate 為基礎的話，那你會碰到一個問題，HeroKu 會告訴你「There are no commands defined in the “ide-helper” namespace.」，你部署到自己的電腦(localhost)當中是沒有問題的，但是你部署到 HeroKu 的時候卻發生了問題。

> [GitHub - rappasoft/laravel-5-boilerplate: A Laravel 5 Boilerplate Project - http://laravel-boilerplate.com](https://github.com/rappasoft/laravel-5-boilerplate)

![https://i.imgur.com/w2nHA5i.png](https://i.imgur.com/w2nHA5i.png)
There are no commands defined in the “ide-helper” namespace.

## 什麼是 ide-helper？他害我沒辦法部署！
基本上 Error Log 是在告訴你說專案可能在 composer 的 post-autoload-dump 這個階段出問題了，稍微查了一下 composer 文件，這個 function 的功能是「發生在自動載入器倒出之後，不管是在 install/update 期間，或經由 dump-autoload 命令。」，簡單來說 HeroKu 在一開始運行時會運行 `composer install` 這項指令，然後開始下載整個專案所有會用到的套件，下載完了以後，將會依序執行 post-autoload-dump 的所有指令，而 HeroKu 就是在這些指令當中出錯了，疑似是 `@php artisan ide-helper:generate` 這項指令出錯了。

![https://i.imgur.com/0xTKKg0.png](https://i.imgur.com/0xTKKg0.png)
composer.json

Ide-helper 是一種會自動產生 IDE 文件的套件，簡單來說如果 composer 運行這些指令的話，ide-helper 會自動產生出一些魔術方法的文件給 IDE 讀取，讓你在開發的時候更為方便，語法提示跳得更多、提供更多的自動補完功能，但對於伺服器而言，這些文件是不必要的，因此你在部屬專案的時候，可以**將 ide-helper 給拿掉（但沒辦法註解，因為 json 沒有註解)，你就可以正常將專案部署到 HeroKu 當中了**。

- `@php artisan ide-helper:generate`
	- 會產生 `_ide_helper.php` 這份文件
- `@php artisan ide-helper:meta`
	- 會產生 `.phpstorm.meta.php` 這份文件

> [GitHub - barryvdh/laravel-ide-helper: Laravel IDE Helper](https://github.com/barryvdh/laravel-ide-helper)

---

## 參考文獻

> Wikipedia contributors. (2019, April 13). Heroku. In *Wikipedia, The Free Encyclopedia*. Retrieved 01:30, May 7, 2019, from [https://en.wikipedia.org/w/index.php?title=Heroku&oldid=892252710](https://en.wikipedia.org/w/index.php?title=Heroku&oldid=892252710) 

> Composer contributors. (2019, April 13). Composer. In *指令碼- Composer 正體中文文件*. Retrieved 01:30, May 7, 2019, from [https://getcomposer.ycnets.com/doc/articles/scripts.md](https://getcomposer.ycnets.com/doc/articles/scripts.md)

> barryvdh contributors. (2019, April 13). barryvdh. In * [barryvdh](https://github.com/barryvdh) / [laravel-ide-helper](https://github.com/barryvdh/laravel-ide-helper) *. Retrieved 01:30, May 7, 2019, from [GitHub - barryvdh/laravel-ide-helper: Laravel IDE Helper](https://github.com/barryvdh/laravel-ide-helper)