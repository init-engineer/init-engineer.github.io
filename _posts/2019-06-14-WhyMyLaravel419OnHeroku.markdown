---
date            : 2019-06-14 18:00:00
layout          : post
title           : 在 HeroKu 上的 Laravel 專案突然爆掉 419 page expired 的解決方案
subtitle        : 大大救命啊啊啊啊啊啊！我的網站剛剛明明是好的，現在卻壞了，但我都沒有做任何事情，他就突然壞了 ...
description     : "大大救命啊啊啊啊啊啊！我的網站剛剛明明是好的，現在卻壞了，但我都沒有做任何事情，他就突然壞了 ..."
image           : https://i.imgur.com/HDYoWFy.png
optimized_image : https://i.imgur.com/HDYoWFy.png
category        : tutorial
tags            :
- Laravel
- Page Expired
author          : kantai235
origin_url      : https://kantai235.github.io/WhyMyLaravel419OnHeroku
---

# 可憐的案例

> 大大救命啊啊啊啊啊啊！我的網站剛剛明明是好的，現在卻壞了，但我都沒有做任何事情，他就突然壞了！我把 Session 清掉，把 Cache 也清掉，把所有暫存都清掉，他終究告訴我 419 Page Expired，我把整個 HeroKu 都重啟，甚至整個專案都回滾到最初始的狀態，他都沒辦法救活，結果在本機端、開測試機卻正常運行，明明一模一樣的程式碼、一模一樣的所有配置啊！為什麼只有正式機會掛掉？跪求解答 20 點，急急急急急急。
> - 資料來源來自我的案發經歷

最近在部署專案到 HeroKu 上的時候，發生這樣莫名其妙的案例，於是將整個案例寫成文檔紀錄，之前介紹過把 Laravel 專案部署到 HeroKu 上的方法，這次要來分享在 HeroKu 上維護專案時，所發生的離奇案件。

> 將 Laravel 部署到 HeroKu 上的教學: [如何將你的 Laravel boilerplate 專案成功部署到 HeroKu · 乾太 Kantai](https://kantai235.github.io/2019/05/07/HowToBulidLaravelToHeroKu/)

---

# 419 Page Expired

![https://i.imgur.com/bEZn7DD.png](https://i.imgur.com/bEZn7DD.png)

基本上有學過 Laravel 的大家們，都知道 419 這個錯誤跟 CSRF 有關，我們正常會認為這可能是前台的 CSRF 過期了，導致使用者運行時把 TOKEN 丟到後端，中介層認為這 TOKEN 過期或無法驗證通過，而 Response HTTP CODE 419，所以在此我有一個大膽的想法，那就是去 `VerifyCsrfToken.php` 把所有的路由都取消 CSRF 認證，大概是這個樣子:

```php
...
    /**
     * The URIs that should be excluded from CSRF verification.
     *
     * @var array
     */
    protected $except = [
        '*',
    ];
...
```

> 在 Laravel 當中如何取消 CSRF 的認證: [在 Laravel 當中，如何移除特定 Router 的 CSRF？ · 乾太 Kantai](https://kantai235.github.io/2018/09/26/ExcludingRoutesFromTheCSRFMiddleware/)

取消 CSRF 驗證後，表單成功送出去了，伺服器也沒有出現 419 Page Expired，但結果是伺服器並沒有做任何與資料新增有關的動作，這就奇怪了呀，所以使用者端沒問題，伺服器也是沒問題的，最有可能有問題的地方可能在於資料庫這個部分，這時候我們打開 HeroKu JawsDB Maria 的 Plans 定價頁面(這邊我們以 JawsDB Maria 為例)。

HeroKu JawsDB Maria: [JawsDB Maria - Add-ons - Heroku Elements](https://elements.heroku.com/addons/jawsdb-maria)

![https://i.imgur.com/JJ88v86.png](https://i.imgur.com/JJ88v86.png)

HeroKu 會告訴你 Storage Capacity 5 MB，表面上意思是整個資料庫的容量給你 5 MB 的大小，但實際上可能是寫入的量，所以才會導致刪除可以運行，但要寫入新的資料就無法，而網站運行一段時間後，因為寫入的量過多了，所以 CSRF 的 Token 無法繼續寫入資料庫的 Sesstion 當中，而 Laravel 在驗證 CSRF 的時候找不到相對應的資料，才會回覆你 419 Page Expired。

# 解決方案
1. 課金
就這麼簡單，你只要把你的 HeroKu JawsDB 的 Plans 方案從 Free 升級，問題馬上就解決了。

2. 改其他 Database
今天的問題主要是你目前連線的 Database 無法繼續寫入資料了，你只要改其他的服務，無論是 AWS、GCP 或自架，就可以解決這問題。

---

# 後記

這是一項很奇耙的問題，解決方案很簡單，但卻無法得知為什麼而出錯，找出解決方案有點靠經驗、觀落陰去解決，最重要的是 HeroKu 並沒有任何通知，他並不會寄封 Email 或系統跳通知來通知你的 Database 某項限制快抵達上限，這點我覺得是 HeroKu 需要修改的方向，不過對於開發、維護人員來講，可能先能夠找到問題、解決問題，才是首要目標吧，所以寫了這篇來紀錄。