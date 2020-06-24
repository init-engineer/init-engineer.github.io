---
date            : 2019-03-24 12:00:00
layout          : post
title           : EP1. 我要怎麼申請 Facebook API？好像很難又好像很簡單欸！
subtitle        : 在先前我們討論了如何透過 API 來將文章發表到社群平台當中、如何透過 API 來將社群平台的文章刪除 ...
description     : "在先前我們討論了如何透過 API 來將文章發表到社群平台當中、如何透過 API 來將社群平台的文章刪除 ..."
image           : https://i.imgur.com/yRuDHCb.png
optimized_image : https://i.imgur.com/yRuDHCb.png
category        : tutorial
tags            :
- Facebook API
author          : kantai235
origin_url      : https://kantai235.github.io/EP1HowDoYouRelyOnTheKaobeiEngineer
---

在先前我們討論了如何透過 API 來將文章發表到社群平台當中、如何透過 API 來將社群平台的文章刪除，但我們中間略過了申請 API 的部分，所以接下來我們將要介紹如何申請相關社群的 API、申請經驗談、常用工具。

- EP1. 我要怎麼申請 Facebook API？好像很難又好像很簡單欸！(本篇)
- EP2. 我要怎麼申請 Twitter API？好像很難又好像很簡單欸！
- EP3. 我要怎麼申請 Plurk API？好像很難又好像很簡單欸！

---

## 簡介

本篇以 Facebook 為主，你需要先擁有 Facebook 的帳號，相信這部分大家都很熟悉，知道如何申請帳號，所以整篇文章會著重在如何申請應用程式、測試 API、提交應用程式審查，總共三個環節。

- Facebook 應用程式 - 申請
- Facebook 應用程式 - 測試
- Facebook 應用程式 - 審查

---

## Facebook 應用程式 - 申請

我們直接透過 Facebook 開發者網站(連結如下)進入 Facebook 專給開發者的「我的應用程式」，去申請一個 Facebook 的應用程式。

> - 網站連結：[所有應用程式 - Facebook for Developers](https://developers.facebook.com/apps/)

![https://imgur.com/NdcQIJh.png](https://imgur.com/NdcQIJh.png)
所有應用程式 - Facebook for Developers

然後直接點選「新增應用程式」，Facebook 會要求你輸入兩個簡單的資訊，在這邊的顯示名稱、聯絡電子郵件事後都可以更改，顯示名稱顧名思義就是顯示的名稱，而聯絡電子信箱的用途在於如果應用程式審核通過/未通過之類的情況時，會同步寄信到連絡信箱當中。

![https://imgur.com/6tXacN7.png](https://imgur.com/6tXacN7.png)
建立新的應用程式

接下來我們就建立了一個 Facebook 應用程式了，你已經成功向前跨出一步，不過接下來我們還要填寫許多資料，我們點選「設定 > 基本設定」，去填寫其他詳細的資訊。

![https://imgur.com/I9FR6Fz.png](https://imgur.com/I9FR6Fz.png)
Facebook 應用程式 主控板

這邊你可以獲得幾些重要的參數，例如應用程式編號、應用程式密鑰，順便也可以把其他資訊也填寫一下，例如應用程式網域、隱私政策網址、類別，資訊填寫得越完整的話，對於後續審核也會比較有所幫助(或許啦)。

- 應用程式編號 = `FACEBOOK_APP_ID`
- 應用程式密鑰 = `FACEBOOK_APP_SECRET`

![https://imgur.com/S8ZvECI.png](https://imgur.com/S8ZvECI.png)
Facebook 應用程式 設定 基本資料

往下拉還有聯絡資料，是針對歐盟所需要填寫的，根據 Facebook 的說法是這樣：

> 一般資料保護規章（GDPR）規定在歐盟營業的特定公司指派一位資料保護長，一般大眾有關於其資料如何處理的問題時，可以聯絡該人員以取得資訊。
> 此聯絡資料以及其他有關你應用程式或網站的資訊都將提供給 Facebook 用戶。

你可以如實填寫，也可以像我一樣不填寫，不過接下來的平台就真的必須填寫了，我們是以網站後端 Call API 的方式來使用這應用程式，所以理所當然我們要新增「網站」做為我們的平台。

![https://imgur.com/Ms7YsAl.png](https://imgur.com/Ms7YsAl.png)
Facebook 應用程式 設定 基本資料

在進階設定的地方也有其他的詳細資訊可以設定，如果你對於安全上比較吹毛求疵，你也可以把這些資訊給設定一下。

![https://imgur.com/UOIMZtl.png](https://imgur.com/UOIMZtl.png)
Facebook 應用程式 設定 進階

---

## Facebook 應用程式 - 測試

基本上申請應用程式不太會遇到什麼問題，在使用上、申請審核才會碰到比較多問題，尤其是申請審核的部分，這部分我們後面會再提到。

那我們接下來要來測試一下 Facebook 的 API 應該如何使用，你可以透過頂端導覽列的「工具」來看到三個主要的工具項目，不過我們應該會時常用到的分別會是「圖形 API 測試工具」以及「存取權杖偵錯工具」。

- `圖形 API 測試工具` 你在測試 API 時會時常用到。
- `存取權杖偵錯工具` 如果你希望延長 Token 的授權期限、檢查 Token 所擁有的權限時，也會時常用到。

> - [Facebook 開發人員文件 | Facebook API、SDK、指南](https://developers.facebook.com/docs?locale=zh_TW)

![https://imgur.com/unPZ3Pr.png](https://imgur.com/unPZ3Pr.png)
Facebook 圖形 API

我們先來測試看看圖形 API 測試工具，會有「Facebook 應用程式」、「用戶或粉絲專頁」這兩個下拉式選單，你需要選擇你的應用程式，以及你的操控目標，接下來才能使用 API 測試。

![https://imgur.com/UCjq9xL.png](https://imgur.com/UCjq9xL.png)
圖型 API 測試工具

在選擇完畢之後，他會告知你目前有哪些權限，基本上只會有「`public_profile`」這個獲得基本資訊的權限，如果你的應用程式是用來發表、刪除粉絲專頁文章的話，你會需要「`manage_pages`」這項權限，然而根據 Facebook 所只是，如果你需要 `manage_pages` 這項權限的話，你還另外需要「`publish_pages`」這項權限，才能以粉絲專頁的身分發佈文章。

> - 關於 manage_pages ...
> 
> 針對用戶所管理的粉絲專頁和應用程式，讓應用程式能夠擷取粉絲專頁存取權杖。
> 應用程式需要 manage_pages 和 publish_pages 才能以粉絲專頁的身分發佈。
> 
> - [manage_pages - 文件 - Facebook for Developers](https://developers.facebook.com/docs/facebook-login/permissions/#reference-manage_pages)

> - 關於 publish_pages …
> 
> 同時具備 manage_pages 權限時，應用程式能夠以應用程式用戶所管理的任何粉絲專頁身分發佈貼文、留言和按讚。
> 應用程式需要有 manage_pages 和 publish_pages 權限，才能以粉絲專頁的身分發佈內容。
> 此項權限無法讓您以個人帳號發佈內容。
> 
> - [publish_pages - 文件 - Facebook for Developers](https://developers.facebook.com/docs/facebook-login/permissions/#reference-publish_pages)

![https://imgur.com/u4UXFoz.png](https://imgur.com/u4UXFoz.png)
存取權杖設置

當你測試完 API，並且獲得你呼叫 API 時需要的 ACCESS TOKEN 時候，我們可以先去「`存取權杖偵錯工具`」檢查一下這個 TOKEN 的詳細資訊，你會發現他的有效期限非常短，不過我們可以透過「延伸存取權杖」來將有效期限延長。

![https://imgur.com/phVUcZu.png](https://imgur.com/phVUcZu.png)
存取權杖偵錯工具

透過「延長存取權杖」可以將有效期限延長至兩個月。

![https://imgur.com/E2BrzC8.png](https://imgur.com/E2BrzC8.png)
延伸存取權杖

不過 Facebook 有個很惱人的地方，以社群平台發表文章這件事為例子，如果你的應用程式發佈狀態是「關閉」的話，你所有透過該應用程式所發表的文章都無法被其他人看到，只有你自己看得到，因此我們需要將應用程式的狀態改為「開啟」。

![https://imgur.com/yA5bTef.png](https://imgur.com/yA5bTef.png)
Facebook 應用程式 狀態

不過這麼做會遇到一個問題，我們分成幾個層面來講解，任何透過應用程式所發佈的文章，會在公開、不公開之間切換，會變成下面這兩種情況：

- 狀態`關閉`：應用程式所發表的文章（包含曾經發表的文章）僅有應用程式相關人員（管理者、測試人員）可以正常瀏覽、點讚、分享。
- 狀態`開啟`：應用程式所發表的文章（包含曾經發表的文章）所有人都可以正常瀏覽、點讚、分享。

再來是`權限`上的問題，應用程式的開啟與關閉狀態所能獲得的權限皆不同，會有下面兩種狀況：

- 狀態`關閉`：你可以無視審查結果，獲取所有 Facebook 所提供的權限。
- 狀態`開啟`：你只能獲得 Facebook 審查通過的權限。

聰明的你應該會想到一個方法想來嚐試鑽漏洞，就是將應用程式狀態設定為關閉，然後去獲得你所需要的權限、TOKEN，並且將 TOKEN 延長有效期限，再來去將應用程式開啟，這樣就可以獲得擁有權限且文章公開的 TOKEN 了！

但這個想法是沒辦法的，你的 TOKEN 雖然不會被收回去，但這個 TOKEN 尚未審查的權限都會被註銷，所以終究還是要將應用程式提交審查才能解決這問題。

---

## Facebook 應用程式 - 審查

基本上應用程式最後都會走到審查的這個步驟，不然你開發的應用程式將沒辦法正常的使用，所以我們回到應用程式設定當中，有個「應用程式審查」的選項，這邊可以看到每項權限的功能、獲取狀態以及需求條件 ... 等等訊息，也可以從這邊申請你所需要的權限。

如果以透過粉絲團發表文章這項功能來說的話，你需要申請 `manage_pages` 以及 `publish_pages` 這兩項權限。

> - manage_pages 審查條件
> 
> 如果您的應用程式要求此項權限，Facebook 將會 [審查](https://developers.facebook.com/docs/apps/review/login) 應用程式運用該權限的方式。您可代表列於應用程式主控板「角色」部分的用戶授予此項權限，而無須經過 Facebook 審查。
> 提交審查時，請務必讓我們的團隊能輕鬆重現您的指示。舉例來說，如果您的「粉絲專頁管理工具」有自己的驗證系統，請務必提供有效的登入方式（如用戶名稱／密碼），以便審查團隊使用您的工具並測試其功能。
> 
> [manage_pages - 文件 - Facebook for Developers](https://developers.facebook.com/docs/facebook-login/permissions/#reference-manage_pages)

> - publish_pages 審查條件
> 
> 如果您的應用程式要求這項權限，Facebook 必須 [審查](https://developers.facebook.com/docs/apps/review/login) 應用程式運用此權限的方式。
> 透過「應用程式審查」要求此項權限時，請務必提供詳細指示，以便我們的團隊輕鬆重現。
> 
> [publish_pages - 文件 - Facebook for Developers](https://developers.facebook.com/docs/facebook-login/permissions/#reference-publish_pages)

![https://imgur.com/5uB61e9.png](https://imgur.com/5uB61e9.png)
Facebook 應用程式審查 - 權限和功能

如果你要求權限的話，你會需要提供一些資料給 Facebook 審查團隊，像是使用情境說明、步驟說明、畫面錄影甚至是提供測試帳號給 Facebook 審查團隊來測試使用，舉例來說，我申請 `manage_pages` 以及 `publish_pages` 這兩項權限的時候，我提交了以下內容給 Facebook：

> 這項應用程式是我個人要使用的，我想透過我的應用程式來方便我個人管理粉絲團，執行發布文章、刪除文章等各種管理的動作。
> 1. 透過 BitBucket、GitHub 或帳號密碼的方式登入本平台。
> 2. 撰寫發文內容、選擇樣式與字型，並且按下發表文章。
> 3. 本平台自動將文章發表至本平台的粉絲團。
> ---
> 平台網址：[純靠北工程師 | 首頁](https://kaobei.engineer)
> ---
> 本平台的粉絲團：
> [純靠北工程師 - 首頁 | Facebook](https://www.facebook.com/kaobei.engineer)
> [純靠北工程師 kobeengineer - 首頁 | Facebook](https://www.facebook.com/init.kobeengineer)
> ---
> 帳號: ＿＿＿＿＿＿＿＿＿
> 密碼: ＿＿＿＿＿＿＿＿＿

除此之外還有整個發文流程的影片錄影、每個流程的詳細說明。

![https://imgur.com/uuSs01b.png](https://imgur.com/uuSs01b.png)
Facebook 應用程式審查 - 目前的要求

不過儘管如此，Facebook 審查團隊有時候還是會打槍拒絕，這時候你可以修正你的流程，或者提出異議給審查團隊，提交問題時可以使用中文來撰寫問題描述，會有委外翻譯團隊負責將你的問題翻譯成英文，而審查團隊處理完畢後，雖然也是用英文來回覆，但委外翻譯團隊也會將回覆翻譯成中文，我自己個人覺得 Facebook 在這部分做得很好。

![https://imgur.com/BLCngBO.png](https://imgur.com/BLCngBO.png)
Facebook 應用程式審查 - 被拒絕惹 QQ

不過 Facebook 審查團隊有時候會跳針，例如像下面這問題描述，Facebook 團隊是覺得可以批准應用程式，但希望提供測試帳號給 Facebook 團隊使用，當我補上測試帳號之後，Facebook 的審查者換別人了，審查者卻又以「`您的螢幕錄影檔案並未說明使用此權限如何能直接改善您應用程式的用戶體驗。此外，我們也無法藉由手動測試應用程式來判斷。`」來拒絕，這時候就會覺得很莫名其妙。

![https://imgur.com/OmAzTYX.png](https://imgur.com/OmAzTYX.png)
Facebook 應用程式審查

---

## 結語

總而言之，如果落數 Facebook、Twitter、Plurk 這三個社群平台的應用程式申請的話，我個人是任為 Facebook 最不親民、最麻煩的社群平台了，不僅 TOKEN 有期限限制，還有權限需經審核過於麻煩，每位審查人員對於審核標準不統一，不過也不是沒有優點，特別委外翻譯人員來協助翻譯問題回報、申訴，我覺得這點是大大加分的。

---

## 參考資料

> - [所有應用程式 - Facebook for Developers](https://developers.facebook.com/apps/)
> - [Graph API - 文件 - Facebook for Developers](https://developers.facebook.com/docs/graph-api?locale=zh_TW)
> - [manage_pages - 文件 - Facebook for Developers](https://developers.facebook.com/docs/facebook-login/permissions/#reference-manage_pages)
> - [publish_pages - 文件 - Facebook for Developers](https://developers.facebook.com/docs/facebook-login/permissions/#reference-publish_pages)