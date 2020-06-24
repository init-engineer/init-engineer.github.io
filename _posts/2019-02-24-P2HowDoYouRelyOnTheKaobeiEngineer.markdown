---
date            : 2019-02-24 12:00:00
layout          : post
title           : P2. 純靠北工程師怎麼做的？就只是個簡單的資料庫模型而已。
subtitle        : 既然是工程師，那網站當然要自幹啊！在開始介紹如何寫功能以前，要先通過兩項前置任務（如果已經有點軟體架構概念、資料庫基本知識，則可以直接跳過） ...
description     : "既然是工程師，那網站當然要自幹啊！在開始介紹如何寫功能以前，要先通過兩項前置任務（如果已經有點軟體架構概念、資料庫基本知識，則可以直接跳過） ..."
image           : https://i.imgur.com/lUfXC3X.png
optimized_image : https://i.imgur.com/lUfXC3X.png
category        : tutorial
tags            :
- Laravel
author          : kantai235
origin_url      : https://kantai235.github.io/P2HowDoYouRelyOnTheKaobeiEngineer
---

既然是工程師，那網站當然要自幹啊！在開始介紹如何寫功能以前，要先通過兩項前置任務（如果已經有點軟體架構概念、資料庫基本知識，則可以直接跳過）：

- P1.純靠北工程師怎麼做的？就只是個簡單的後端架構而已。
- `P2.純靠北工程師怎麼做的？就只是個簡單的資料庫模型而已。(本篇)`

---

## 怎麼做資料庫模型？

有在做資料庫規劃的話，基本上看了 `ER-Model` 就知道該如何使用了，如果你對資料庫這方面不太熟悉，但又可能有機會需要用到，那麼你會需要稍微了解一些眉眉角角，在資料庫的世界稱之為「::正規化::」，而正規化有第一正規化、第二正規化、第三正規化、BC正規化 ... 到第六正規化，不過會建議至少要符合第三正規化、BC正規化，對於整個資料庫的效能來說有非常顯著的改善。

> ER-Model 科普一下 [ER模型 - 維基百科，自由的百科全書](https://zh.wikipedia.org/zh-tw/ER%E6%A8%A1%E5%9E%8B)

- 第一正規化	-> 去除重複性
- 第二正規化	-> 去除部分相依
- 第三正規化	-> 去除間接相依
- BC 正規化	-> 複合主鍵需皆為外來鍵

> [!] 資料庫正規化 科普一下
> - -> [第一正規化 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/%E7%AC%AC%E4%B8%80%E6%AD%A3%E8%A6%8F%E5%8C%96)
> - -> [第二正規化 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/%E7%AC%AC%E4%BA%8C%E6%AD%A3%E8%A6%8F%E5%8C%96)
> - -> [第三正規化 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/%E7%AC%AC%E4%B8%89%E6%AD%A3%E8%A6%8F%E5%8C%96)
> - -> [BC正規化 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/BC%E6%AD%A3%E8%A6%8F%E5%8C%96)

基本上資料庫設計至少會做到第三正規化，除非有特殊需求才會繼續往上做，不過對我而言我是會做到 BC 正規化，所以接下來我們就以純靠北工程師的資料庫作為範例，把資料庫設計出來。

首先我們一定會有一張表，那張表可能紀錄著文章相關資訊，每篇文章一定會對應一個發文者，所以還需要紀錄發文者相關資訊，再來每篇文章可能會有零獲多筆留言的內容，所以我們需要把每筆留言都記錄下來，最好順便告訴我總共有幾筆留言，整張表最後看起來大概會像下面這張表：

![https://i.imgur.com/KPqBdNT.png](https://i.imgur.com/KPqBdNT.png)
Table - Sample 1

設計完之後，這樣的資料表顯然不是很好，你在取用資料的時候，可能會拿到太多你可能不需要的資料，造成 Network I/O 上不必要的浪費，甚至紀錄了許多重複的資料，像是使用者資料，如果使用者發了 10 篇文章，那這個使用者的資料就被重複寫入 10 遍，種種不理想的原因，所以我們要開始來做正規化這件事，首先我們要做第一正規化，將去除重複性，但意思不是把重複的資料刪光光！像是上面那張表的留言、留言內容 ... 那個欄位有可能有零或多筆留言，因此我們需要把他的資料切開，效果如下：

![https://i.imgur.com/uZunocZ.png](https://i.imgur.com/uZunocZ.png)
Table - Sample 2

乍看之下好像寫入的資料量變多了欸！但接下來的才是重點，通常我會把第二正規化與第三正規化一起做，去除部分相依、去除間接相依，今天一張資料表是負責儲存使用者資料，就應該只儲存使用者資料，而不是把其他有點關係但不相干的資料也儲存進來，按照這樣的邏輯，我們會切成三張表，分別是文章資料表、使用者資料表、留言資料表，資料表樣本如下：

![https://i.imgur.com/VX2BG5r.png](https://i.imgur.com/VX2BG5r.png)
Table - Sample 3

在這時候還有一個概念，就是如果這個欄位的內容可以透過其他欄位或其他表格的欄位算出來，那麼這個欄位就不應該被儲存，就像是留言資料表的留言數量，留言數量可以透過 `GROUP BY` 文章編號來 `COUNT` 出來，那麼留言數量這個欄位就是多餘的，可以被刪除。

![https://i.imgur.com/MPN3IfH.png](https://i.imgur.com/MPN3IfH.png)
Table - Sample 4

接下來要講的 BC 正規化是複合主鍵需皆為外來鍵，我們可以擴增一個功能來當作範例，目前是一篇文章會固定搭配一張圖片，但我們讓功能更完善些，一篇文章可以搭配零或多張圖片，首要步驟是將圖片資訊切出來作為獨立的資料表，並且在文章資料表與圖片資料表當中有個連接這兩張資料表的關聯表，關聯表會以這兩張資料表的主鍵作為外來鍵參考，形成複合主鍵，同時也符合 BC 正規化的要求。

> - [!] 主鍵(Primary Key) 科普一下 -> [SQL 主鍵 - 1Keydata SQL 語法教學](https://www.1keydata.com/tw/sql/sql-primary-key.html)
> - [!] 外來鍵(Foreign Key) 科普一下 -> [SQL 外來鍵 - 1Keydata SQL 語法教學](https://www.1keydata.com/tw/sql/sql-foreign-key.html)
> - [!] 關聯表 科普一下 ->
> - 關聯表是兩張資料表的中間，在建立一張資料表作為兩張表之中的關聯，如果你不想改變原本表格的結構，那麼關聯表可以在不改變原表格之下，獨立儲存額外的資料，舉例來說，你的資料庫當中有產品資料表、訂單資料表，你的產品資料表記錄著產品名稱、定價 ... 等等相關資訊，而訂單資料表記錄著客戶資訊、出貨日期、訂單日期 ... 等等資訊，然而每個產品可能都有不同折扣，且每筆訂單都有多個產品，這時候你就可以在產品與訂單資料表當中，建立一個訂單詳細資料的關聯表，用來記錄產品的單價、折扣，資料表圖片可參考下列連結中的[連結三個資料表以建立多對多關聯性]。
> - [影片：建立多對多關聯性 - Access](https://support.office.com/zh-hk/article/%E5%BD%B1%E7%89%87%EF%BC%9A%E5%BB%BA%E7%AB%8B%E5%A4%9A%E5%B0%8D%E5%A4%9A%E9%97%9C%E8%81%AF%E6%80%A7-e65bcc53-8e1c-444a-b4fb-1c0b8c1f5653)

![https://i.imgur.com/QurR22A.png](https://i.imgur.com/QurR22A.png)
Table - Sample 5

---

## 資料庫模型的採用

上篇 `[P1. 純靠北工程師怎麼做的？就只是個簡單的後端架構而已。]` 當中，文章尾端有提到純靠北工程師是以一項 `[Laravel 5 Boilerplate]` 專案下去修改而成的，他本身就擁有 `RBAC (以角色為基礎的存取控制)` 的設計，這方面讓我們在角色與權限上的管控可以更為方便。

> [!] RBAC 科普一下 -> [以角色為基礎的存取控制 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/%E4%BB%A5%E8%A7%92%E8%89%B2%E7%82%BA%E5%9F%BA%E7%A4%8E%E7%9A%84%E5%AD%98%E5%8F%96%E6%8E%A7%E5%88%B6)

> [GitHub - rappasoft/laravel-5-boilerplate: A Laravel 5 Boilerplate Project - http://laravel-boilerplate.com](https://github.com/rappasoft/laravel-5-boilerplate)

![https://i.imgur.com/0ge8979.png](https://i.imgur.com/0ge8979.png)
Laravel 5 Boilerplate 的 ER-Model

這樣的做法可以讓你在人員上的管理更為直覺、輕鬆，舉例來說今天如果純靠北工程師希望多些管理員來一同維護，我只要賦予使用者 Social Cards Administrator(管理文章的角色)，這名使用者就擁有相關的權限了，如果今天有使用者發了不適當的文章，除了直接把使用者 BAN 掉以外，也可以選擇直接把使用者的發文權限給拿掉。

![https://i.imgur.com/aOH7prd.png](https://i.imgur.com/aOH7prd.png)
純靠北工程師的 Role Management

---

## 加入文章的 Table

接下來我們要開始添加些平台會需要用到的資料表，首當其衝就是文章(social_cards)了，這張資料表會需要紀錄誰發表的、文章內容是什麼、文章是否啟用、社群平台的文章 id、社群平台的讚數量、社群平台的分享數量，除此之外為了有軟刪除的功能，要加上新增時間、修改時間、刪除時間，最好再加上誰刪除的、刪除原因。

> 軟刪除 科普一下 [Eloquent: Getting Started - Laravel - The PHP Framework For Web Artisans](https://laravel.com/docs/5.7/eloquent#soft-deleting)

![https://i.imgur.com/8qmNiut.png](https://i.imgur.com/8qmNiut.png)
加上 social_cards

---

## 加入紀錄圖片的 Tabel

有時候使用者是上傳自己的圖片來發文，這時候我們就需要有張表來記錄使用者這張圖被儲存的位置，以及這張圖的相關資訊(例如上傳者、紀錄時間、軟刪除 ... 等等)。

這邊圖片資訊的部分，我們要考慮到多磁碟管理，所以需要再細切一下欄位：

- `disk` 來自哪個硬碟。
- `file_path` 檔案的相對路徑。
- `file_name` 檔案的名稱。
- `file_type` 檔案的副檔名。

這時候你可能會衍生出一個問題說：這樣我在 View 取用這張圖片的時候，我不就要寫成像下面這樣子嗎？這樣很醜欸！

```php
...

	$image->disk . $image->file_path . $image->file_name . $image->file_type

...
```

我會推薦大家透過 `Eloquent` 的 `Attribute` 來解決這問題，並且結合 `sprintf` 來把路徑拼湊成完整的位址，舉例來說：

```php
    /**
     * @return string
     */
    public function getUrlAttribute() {
        return sprintf(
				'%s/%s.%s',
				$this->file_path,
				$this->file_name,
				$this->file_type
			);
    }
```

> [!] Eloquent - Attribute 科普一下 -> [Eloquent: Mutators - Laravel - The PHP Framework For Web Artisans](https://laravel.com/docs/5.7/eloquent-mutators#defining-an-accessor)

![https://i.imgur.com/JsqlucM.png](https://i.imgur.com/JsqlucM.png)
加上 cards_image

---

## 記錄所有社群平台留言的 Table

接下來為了能夠記錄每個社群平台的貼文留言，我們需要再新增一個負責記錄留言的 Tabel，分別記錄是哪篇貼文、來自哪個社群平台、留言者的社群 id、留言內容，比較需要注意的是 Facebook 這個社群平台有「留言中的留言」，所以會需要多一個 reply_by 的欄位，負責記錄這是回覆哪比留言的。

![https://i.imgur.com/MvbeEzw.png](https://i.imgur.com/MvbeEzw.png)
加上 social_comments

---

## 加入 Issues 所需的相關 Table

這是自幹一個 Issues 問題回報系統的時候，所設計的簡易資料表，首先會有主體 issues 用來記錄誰回報的、問題內容、是否被關閉，以及運用上面 reply_by 的概念，就可以少建一個結構相似的表。

而 Issues 問題回報都會需要標上 Tag 標籤，像是 Bug、Wish、Question … 等等的標籤，所以會需要 labels 以及與 issues 之間的關聯表 issues_has_labels。

到這邊為止就差不多可以依據這樣的資料庫設計，開發出純靠北工程師了，相關的功能都有所需的資料表了。

![https://i.imgur.com/CmWiD6T.png](https://i.imgur.com/CmWiD6T.png)
加上 issues 需要的 table，分別為 issues、issues_has_labels、labels

---

## 下一步為文章與圖片加個關聯表

這時候有個問題，每篇文章都固定會有一張圖片，但可不可以支援純文字發表或多圖片發表？這時候就需要在 social_cards 與 cards_image 之間多放個關聯表 card_has_images，一篇文章可以有零個或多個的圖片，而圖片只會對應到唯一的貼文。

![https://i.imgur.com/mMcmHSH.png](https://i.imgur.com/mMcmHSH.png)
讓文章格式並不僅限於一張附圖

---

## 結語

在開發系統的時候，多多少少避免不了資料庫，無論是設計還是建置，有可能你有配合的資料庫管理師(DBA)可以為你操刀這一切大小事，但大多數的情況可能沒有，這時候我會很推薦後端工程師多多少少點一下這方面的技能，資料庫易學難精，學好資料庫可以讓你在後續的開發上少掉很多事情，舉例來說如果資料表一開始沒有切好，就如同文章開頭的第一張表那樣，那你可能要在後端為他開發許多 function 來使用，就算你開發好一系列的 function，你接下來會面臨的問題是效能不佳、Network I/O 過於肥大的問題，種種問題我會建議你還是直接把資料庫設計好。

---

## 資料參考

如果你也想嘗試自己寫個靠北 XXX 網站的話，專案的使用你可以參考這個專案，這項專案已經幫你完成很多事情了，例如權限與角色、信箱認證、以社群平台登入串接 ... 等等功能，純靠北工程師也是以這個專案下去修改而成的，這部分在後端架構篇有提到，一天自幹出自己的靠北網站不是夢！

> [GitHub - rappasoft/laravel-5-boilerplate: A Laravel 5 Boilerplate Project - http://laravel-boilerplate.com](https://github.com/rappasoft/laravel-5-boilerplate)