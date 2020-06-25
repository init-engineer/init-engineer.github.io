<p align="center">
	<a href="https://kaobei.engineer"><img src="https://i.imgur.com/iuP8yS0.png" alt="INIT.ENGINEER" width="300"></a>
</p>
<h3 align="center">喜歡純靠北工程師？我們也是。</h3>
<p align="center">這是一份純靠北工程師的專案，請好好愛護它，謝謝。</p>
<p align="center">
    <a href="https://www.facebook.com/init.kobeengineer" title="facebook init"><img src="https://img.shields.io/badge/facebook-@init.kobeengineer-3b5998.svg" alt="facebook init" /></a>
    <a href="https://www.facebook.com/kaobei.engineer" title="facebook testing"><img src="https://img.shields.io/badge/facebook-@kaobei.engineer-3b5998.svg" alt="facebook testing" /></a>
    <a href="https://twitter.com/kaobei_engineer" title="twitter"><img src="https://img.shields.io/badge/twitter-@kaobei_engineer-55acee.svg" alt="twitter" /></a>
    <a href="https://www.plurk.com/kaobei_engineer" title="plurk"><img src="https://img.shields.io/badge/plurk-@kaobei_engineer-ff574d.svg" alt="plurk" /></a>
</p>

---

![預覽圖](https://i.imgur.com/wT0GcYG.png)

---
## 簡介
這是一份純靠北工程師的共同協作部落格專案，主要用途於撰寫開發日誌、營運公告，支線任務是經營技術類型的文章，並導流回原作者的部落格，You WIN、純靠北 WIN、大家 WIN WIN。

---
## 安裝
1. `bundle install` 跑起來！
2. `npm install` 跑起來！
3. `gulp` 跑起來！

---
## 貢獻

### 加入作者名單
1. 在 `_authors` 這資料夾當中新增一個自己的簡介，檔案名稱請以 `{帳號}.md` 的方式命名。
```md
---
layout: author
photo: /assets/img/uploads/profile/{帳號}.jpg
name: 你的帳號
display_name: 你的名字
position: 協同作者
bio: 個人簡介
github_username: YOUR_GITHUB_USERNAME
facebook_username: YOUR_FACEBOOK_USERNAME
twitter_username: YOUR_TWITTER_USERNAME
instagram_username: YOUR_INSTAGRAM_USERNAME
linkedin_username: YOUR_LINKEDIN_USERNAME
medium_username: YOUR_MEDIUM_USERNAME
plurk_username: YOUR_PLURK_USERNAME
---
```
2. 請將你的大頭貼上傳至 `/assets/img/uploads/profile/{帳號}.jpg`

### 部署文章
1. 在 `_posts` 這資料夾當中新增一個自己想部署的文章，檔案名稱請以 `yyyy-mm-dd-{英文標題}.markdown` 的方式命名。
```md
---
date            : yyyy-mm-dd hh:ii:ss
layout          : post
title           : 文章標題
subtitle        : 副標題
description     : "簡要說明"
image           : https://i.imgur.com/xxxxxxx.png
optimized_image : https://i.imgur.com/xxxxxxx.png
category        : tutorial
tags            :
- TAG
author          : 帳號
origin_url      : 你想宣傳的原文網址
---

文章內容 ...

```

---
## 總結

Have fun :)
