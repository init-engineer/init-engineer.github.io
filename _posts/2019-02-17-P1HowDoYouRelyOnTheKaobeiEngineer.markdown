---
date            : 2019-02-14 12:00:00
layout          : post
title           : P1. 純靠北工程師怎麼做的？就只是個簡單的後端架構而已。
subtitle        : 既然是工程師，那網站當然要自幹啊！在開始介紹如何寫功能以前，要先通過兩項前置任務（如果已經有點軟體架構概念、資料庫基本知識，則可以直接跳過） ...
description     : "既然是工程師，那網站當然要自幹啊！在開始介紹如何寫功能以前，要先通過兩項前置任務（如果已經有點軟體架構概念、資料庫基本知識，則可以直接跳過） ..."
image           : https://i.imgur.com/e8Xc1xo.png
optimized_image : https://i.imgur.com/e8Xc1xo.png
category        : tutorial
tags            :
- Laravel
author          : kantai235
origin_url      : https://kantai235.github.io/P1HowDoYouRelyOnTheKaobeiEngineer
---

既然是工程師，那網站當然要自幹啊！在開始介紹如何寫功能以前，要先通過兩項前置任務（如果已經有點軟體架構概念、資料庫基本知識，則可以直接跳過）：

- `P1.純靠北工程師怎麼做的？就只是個簡單的後端架構而已。(本篇)`
- P2.純靠北工程師怎麼做的？就只是個簡單的資料庫模型而已。

---

## 我們舉個壞例子
補充一點軟體架構上的知識，讓你後續閱讀起來更順手、開發更順利，如果用經驗談軟體架構的話，剛學 MVC 時，會把所有的商業邏輯、資料庫邏輯都一股腦的塞到 Controller 控制器當中，舉個發表文章的壞例子來說，使用者在送出請求之後，後端程式大概會這樣寫：

發表文章的事件：
```php
/**
 * Class PostsController.
 */
class PostsController extends Controller {
	 public function index(Request $request) {
		try {
			// Step 1. 把 $request 的資料抓出來，並且驗證符不符合規範
			// Step 2. 開始製作文章與圖片
			try {
				// Step 3. 發表文章到 A 社群平台
			} catch (Exception $e) {
				// Step 3. 如果失敗了的話 ...
			}

			try {
				// Step 4. 發表文章到 B 社群平台
			} catch (Exception $e) {
				// Step 4. 如果失敗了的話 ...
			}

			// Step 5. 上面流程都沒問題的話，把這些資訊都存入資料庫做紀錄
			return ... 
		} catch (Exception $e) {
			// 不可預料的 Error
		}
	}
}
```
壞例子的 Action 大概是這樣

簡單的一個 Action 卻包含了驗證、製圖、發文、寫入資料庫 ... 一大堆的功能，程式碼寫越多就越難以維護與除錯，而且這樣寫的缺點有幾些，我大致整理了幾點：

1. 如果任何一個步驟的過程掛掉了，那麼後面通通都跟著掛掉了。
2. 相同功能的程式碼，你可能在整個專案當中到處都看得到。
3. 你可能會遇到文章已經發表到 A 社群平台，但 B 社群平台並沒有，且資料庫卻沒有這筆紀錄。
4. 程式碼的層數可能比千層派還多。

除了壞例子在後端架構上的問題以外，壞例子還有可能在 View 視圖當中塞入過多顯示邏輯，像是發表文章之後要把結果顯示給使用者看，而在 View 視圖社群平台連結的部分可能會像這樣：

文章訊息的視圖：
```html
...

	@if ( 判斷是否有在 A 社群平台發文 )
		<a href="..." class="h4">
			<span class="badge badge-pill badge-primary mt-2">A 社群平台</span>
		</a>
	@else
		<a href="#" class="h4">
			<span class="badge badge-pill badge-danger mt-2">A 社群平台</span>
		</a>
	@endif

	@if ( 判斷是否有在 B 社群平台發文 )
		<a href="..." class="h4">
			<span class="badge badge-pill badge-primary mt-2">B 社群平台</span>
		</a>
	@else
		<a href="#" class="h4">
			<span class="badge badge-pill badge-danger mt-2">B 社群平台</span>
		</a>
	@endif

...
```
壞例子的 Blade 大概是這樣

硬生生地把顯示邏輯塞入到 Blade 切版當中，會使你的視圖無上限的增肥，也會造成重複的程式碼在各種視圖當中不斷出現。

> [!] MVC 科普一下 -> [MVC - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/MVC)

綜合以上的壞例子，接下來我們要做的就是逐一改善，推演成現在純靠北工程師網站的架構，下圖是壞例子的架構圖：

![https://i.imgur.com/WxH4X7M.png](https://i.imgur.com/WxH4X7M.png)
如果以壞例子為基礎，那麼其架構會是這樣

---

## 把程式碼抽離出來

首先我們要把塞在 Controller 控制器當中處理的商業邏輯、資料庫邏輯抽離出來，以及塞在 View 視圖當中的顯示邏輯給抽離出來。

- `Repostory` 處理資料庫邏輯，例如在資料庫當中寫入一筆文章資料。
- `Service` 處理商業邏輯，例如在社群平台發表文章。
- `Presenter` 處理顯示邏輯，例如社群平台的文章連結。

![https://i.imgur.com/YZQPO5o.png](https://i.imgur.com/YZQPO5o.png)
把商業邏輯、資料庫邏輯與顯示邏輯抽離出來後的架構

---

## 加入些防範措施

如果我們能像 MVVM 一樣，弄個 ViewModel 去制約事件傳入的表單資料，並且做到驗證表單的功能，可以的話再多些判斷使用者登入狀況、使用者所擁有的角色或權限，那我們就可以在 Controller 少掉一大堆重複且功能相近的程式碼，因此我們可以加上 Middleware、Request。

- `Middleware` 驗證使用者身份、角色或權限。
- `Request` 驗證表單傳入的資料格式。
- ~~`Validation` 用來發神經的，功能跟 Request 一模一樣，明明同樣一個 Action 有 Request 就可以了，卻還是硬生生要在 Controller 裡面再寫一次 Validation，真是奇怪的版主，自己嘴自己一下。~~

> [!] MVVM 科普一下 -> [MVVM - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/MVVM)

![https://i.imgur.com/o0GgaFi.png](https://i.imgur.com/o0GgaFi.png)
再加入些 Middleware、Request

---

## 加入監聽器事件

平台的運作總是少不了 Log 日誌，如果可以的話希望收到些事件執行的日誌，甚至每次執行都順便做某件事情，像是使用者登入就紀錄個來源、版主什麼時候把自己的學長 BAN 掉又解 BAN 又 BAN 掉又再解 BAN，這時候我們就會需要 Event 事件與 Listener 監聽器來達成我們的目的。

> [!] 事件與監聽器 科普一下 -> [事件 - Laravel - 為網頁藝術家創造的 PHP 框架](https://laravel.tw/docs/5.2/events)

![https://i.imgur.com/OAwrC4d.png](https://i.imgur.com/OAwrC4d.png)
加入事件與監聽器後

---

## 加入觀察者模式
既然有事件上的監聽，那也就少不了資料庫上的觀察，如果可以的話，`Eloquent ORM` 的新增、刪除、修改及查詢(CRUD)都來觀察一下，最好有連動事件，像是如果有新發表的文章就順便通知一下版主。

> [!] 觀察者 科普一下 -> [Eloquent: Getting Started - Laravel - The PHP Framework For Web Artisans](https://laravel.com/docs/5.7/eloquent#observers)

![https://i.imgur.com/veoUKFi.png](https://i.imgur.com/veoUKFi.png)
再加入觀察者模式

---

## 除了 Presenter 以外，還有 Helpers

Presenter 主要目的是在於處理一些顯示邏輯，而不是把顯示邏輯塞在 View 視圖當中，例如上面壞例子所提到社群平台連結的部分，你就可以把程式碼寫在 Presenter 當中，而在 View 視圖就僅只需要一個 `{!! $post->getSpanLink() !!}` 就解決。

但有些顯示邏輯是到處都會用到的，甚至每個 Presenter 都會有的，就會寫到 `Helpers ` 當中，例如時間 format、Form 表單的送出與取消按鈕。

![https://i.imgur.com/IW5Nf3R.png](https://i.imgur.com/IW5Nf3R.png)
放上 Helpers

---

## 下一步 RESTful

接下來要考慮到之後如果要做前後端分離或 APP 化，會需要開發 API 時候的需求，因此除了 View 視圖以外，我們還會多出一個 Response，這部分就不會有 Presenter 與 Helpers 的使用了，因為沒有畫面，只有資料上的傳遞。

![https://i.imgur.com/gUPnMQ9.png](https://i.imgur.com/gUPnMQ9.png)
除了 View 視圖，我們還要考慮到 RESTful 的開發

---

## 資料標準化

我們會需要前台與後台的資料不一樣，例如文章資訊的 API，前台(使用者)收到的就只會是文章圖片、文章內容與發表時間 ... 等等基本資料，不會收到這篇文章是誰發表的敏感資料，而後台(管理者)則是會收到包含誰發表的敏感資料，這時候我們就需要加上 `Transformer ` 來轉換資料內容。

資料都 OK 了，最後希望每次 Response 的格式都有個規範、規則，像是加上 Hypermedia 之類的，這時候我們就需要加上 `Formatter` 來把資料格式統一化。

- `Transformer` 轉換資料的內容，讓前台與後台所需要的資料不一樣。
- `Formatter` 把資料格式統一化，讓每次 Response 的格式都有個規範、規則。

> [!] Hypermedia 科普一下 [ASP.NET - Building Hypermedia Web APIs with ASP.NET Web API](https://msdn.microsoft.com/en-us/magazine/jj883957.aspx)

![https://i.imgur.com/6IMh2hW.png](https://i.imgur.com/6IMh2hW.png)
為 RESTful 加上 Transformer 與 Formatter

---

## 結語

在開發上如果打從一開始就把架構給定好的話，後續無論維護、追加需求或者任何方面，其實都有非常大的顯著效果，你可能會突然興起腦中激盪想出一個很好的功能，但當你要付諸行動時卻可能因為義大利麵式的程式碼而放棄，如果你沒有因此放棄，那麼接下來你要面對的是一連串必要卻很無用的腦力激盪，起初你的熱情可能會在種種障礙當中一點一滴的消磨殆盡，與其這樣不如前面先麻煩一點，後面就不用為了這些麻煩事而讓你的專案遙遙無期。

---

## 資料參考

因為是以 Laravel 為主，所以多多少少會參考一下大家架構是怎麼定的，然後在參雜一些可能在別的地方所學到的觀念， ~~最後摻在一起做成撒 ...~~，下面兩個是這次寫文章時，又重新找出來的資料參考。

- [Laravel 的中大型專案架構 點燈坊](https://old-oomusou.goodjack.tw/laravel/architecture/)
- [LaravelConf2017 筆記 - HackMD](https://hackmd.io/s/S1dlnXB4Z)

如果你也想嘗試自己寫個靠北 XXX 網站的話，專案的使用你可以參考這個專案，這項專案已經幫你完成很多事情了，例如權限與角色、信箱認證、以社群平台登入串接 ... 等等功能，純靠北工程師也是以這個專案下去修改而成的，這部分在資料庫模型篇會再次提到，一天自幹出自己的靠北網站不是夢！

- [GitHub - rappasoft/laravel-5-boilerplate: A Laravel 5 Boilerplate Project - http://laravel-boilerplate.com](https://github.com/rappasoft/laravel-5-boilerplate)
