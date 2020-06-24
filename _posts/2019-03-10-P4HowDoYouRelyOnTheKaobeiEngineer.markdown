---
date            : 2019-03-10 12:00:00
layout          : post
title           : P4. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(2/2)
subtitle        : 既然是社群匿名發文平台，那主要核心當然是發廢文、講幹話啦！可是這要怎麼做呢？文章管理三步驟：繪圖、發表文章、刪除文章 ...
description     : "既然是社群匿名發文平台，那主要核心當然是發廢文、講幹話啦！可是這要怎麼做呢？文章管理三步驟：繪圖、發表文章、刪除文章 ..."
image           : https://i.imgur.com/lLcn54l.png
optimized_image : https://i.imgur.com/lLcn54l.png
category        : tutorial
tags            :
- Laravel
author          : kantai235
origin_url      : https://kantai235.github.io/P4HowDoYouRelyOnTheKaobeiEngineer
---

既然是社群匿名發文平台，那主要核心當然是發廢文、講幹話啦！可是這要怎麼做呢？文章管理三步驟：繪圖、發表文章、刪除文章。

- P3. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(1/2)
- `P4. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(2/2)(本篇)`
- P5. 純靠北工程師怎麼做的？一顆按鈕就把你的文章 BANG 不見！
- P6. 純靠北工程師怎麼做的？自幹前端一個繪圖、後端一個繪圖！前端篇
- P7. 純靠北工程師怎麼做的？自幹前端一個繪圖、後端一個繪圖！後端篇

---

## 簡介

這篇文章的主軸會環繞於 Facebook、Twitter、Plurk 這三個平台的 API 串接，

- Twitter API - uploadMedia/postTweet 串接
- Plurk API - /APP/Timeline/uploadPicture、/APP/Timeline/plurkAdd 串接

延續上篇 `[P3. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(1/2)]` 的內容，我們接下來要開始著手 Twitter、Plurk 的發文功能了，這部分我們會用到其他兩套第三方 Library，分別是 `thujohn/twitter` 以及 `CQD/qlurk`，相關的安裝教學可以在 `[P3. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(1/2)]` 的 `[前置步驟]` 當中獲得詳細介紹。

- [GitHub - thujohn/twitter: Twitter API for Laravel 4/5](https://github.com/thujohn/twitter)
- [GitHub - CQD/qlurk: A Plurk API library in PHP](https://github.com/CQD/qlurk)

---

## 透過 Twitter API 來發表文章

既然要透過 Twitter 來發表文章，那我們要先申請 Twitter 相關的 App Secret、Access Token ... 之類的呀！這方面的教學，我們將會在 `[EP2. 我要怎麼申請 Twitter API？好像很難又好像很簡單欸！]` 當中詳細解說。

完成前置步驟之後，假設我們已經完成了表單、收到表單 ... 等等動作，現在要把文章發表到 Twitter 平台上了，這時候分成兩個路線，一種是透過查看 Twitter API Docs 去找出自己需要使用的 function，一種是直接透過 Library Docs 去找出自己需要使用的 function，這邊我們兩種都採用，先透過 Twitter API Docs 來找尋我們需要使用的 function，所以我們可以打開 API 文件來看一下。

> [Twitter Developer Platform — Twitter      Developers](https://developer.twitter.com)

我們會在 statuses 當中找到 update 這項 API，內容說明如下：

> - Updates the authenticating user's current status, also known as Tweeting.
> - For each update attempt, the update text is compared with the authenticating user's recent Tweets. Any attempt that would result in duplication will be blocked, resulting in a 403 error. A user cannot submit the same status twice in a row.
> - While not rate limited by the API, a user is limited in the number of Tweets they can create at a time. If the number of updates posted by the user reaches the current allowed limit this method will return an HTTP 403 error.
> - [POST statuses/update — Twitter      Developers](https://developer.twitter.com/en/docs/tweets/post-and-engage/api-reference/post-statuses-update.html)

看來這就是我們需要的 API，我們接下來只需要在 Library 當中尋找看看有沒有 `statuses/update ` 相關的 function，我們最後會在 `StatusTrait.php` 當中找到這段 function：

[CODE01]
```php
 /**
	 * Updates the authenticating user’s current status, also known as tweeting.
	 *
	 * Parameters :
	 * - status
	 * - in_reply_to_status_id
	 * - lat
	 * - long
	 * - place_id
	 * - display_coordinates (0|1)
	 * - trim_user (0|1)
	 * - media_ids
	 */
	public function postTweet($parameters = [])
	{
		if (!array_key_exists('status', $parameters))
		{
			throw new BadMethodCallException('Parameter required missing : status');
		}

		return $this->post('statuses/update', $parameters);
	}
```
`StatusTrait.php - postTweet($parameters = [])`

由此可知我們需要利用 `postTweet` 來發表推文，甚至註解也告訴你參數可以帶入哪些了。

除此之外我們可以透過查看 Library 來尋找我們需要的 function，直接打開 Library 的 GitHub 就可以看到 README.md 當中有附註 Library 所有 function 的說明。

> [GitHub - thujohn/twitter: Twitter API for Laravel 4/5](https://github.com/thujohn/twitter)

仔細查看一下我們會在 Status 這個分類當中看到 `postTweet()` 這個 function，其說明為：

> postTweet() - Updates the authenticating user’s current status, also known as tweeting.

除此之外我們還可以看到 Media 這個分類當中有一個 `uploadMedia()`，這個我們可以用來上傳圖片，並且坎入 Tweet 當中，就會變成圖文發表的模式了。

> uploadMedia() - Upload media (images) to Twitter, to use in a Tweet or Twitter-hosted Card.

充分了解我們所需要的 API function 之後，接下來我們要開始把功能實作出來，跟前篇 `[P3. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(1/2)]` 的 Service、Repository 一樣，我們會將功能實作在 Service 當中，其餘的儲存步驟我們要透過 Repository 來去完成，因此我們在 Service 當中會有一個 `releaseTwitter(SocialCards $social_cards)` 的方法，其功能跟 releaseFacebook 差不多，差別只在於一個是將文章發表至 Facebook 社群平台，另一個則是將文章發表至 Twitter 當中。

[CODE02]
```php
use Twitter;

/**
 * Class SocialCardsService.
 */
class SocialCardsService extends BaseService
{

	/**
	 * @param SocialCards $social_cards
	 * 
	 * @return SocialCards
	 */
	public function releaseTwitter(SocialCards $social_cards) : SocialCards
	{
		// Code ...
	}
}
```
`SocialCardsService - releaseTwitter`

接下來我們需要將發表文章的功能寫入 releaseTwitter 當中，並且順利將文章的圖片透過 `uploadMedia` 來上傳，不過 Twitter 會特別一些，因為 Twitter 不像 Facebook 並沒有限制文章內容的上限，`Twitter 有限制 Content 必須在英文 280 字以下，或者中文 140 字以下`，因此你必須在發文之前檢查內容的長度，舉例來說你可以透過 `mb_strlen` 去判斷內容是否符合長度，如果不符合就透過 `mb_substr` 去切割內容，將剩餘的內容改為「...」。

[CODE03]
```php
public function releaseTwitter(SocialCards $social_cards) : SocialCards {
	if ($social_cards->twitter_card_id == null) {
		try {
			$content = $social_cards->content;
			if (mb_strlen($content, "utf-8") > 48)
				$content = mb_substr($content, 0, 48, "utf-8") . " ...";

			$twitter_uploaded_media = Twitter::uploadMedia([
				'media' => Storage::get('file_url')
			]);

			$twitter_post = Twitter::postTweet([
				'status' => $content,
				'media_ids' => $twitter_uploaded_media->media_id_string
			]);
    
			$social_cards = $this->socialCardsRepostory(...)

			return $social_cards;
		} catch(Exception $e) {
			\Log::error('...');
			return $social_cards;
		}
	} else {
		\Log::error('...');
		return $social_cards;
	}
}
```
`releaseTwitter(SocialCards $social_cards)`

整個步驟是這樣子，我們先透過 `mb_strlen` 來判斷內文的長度大小，如果過長就用 `mb_substr` 來做切割，接下來透過 `Storage::get` 來將圖片抓出來，並且透過 `Twitter::uploadMedia` 將圖片上傳至 Twitter，再透過 `media_id_string` 將圖片連結抓出來，丟給 `postTweet` 的 `media_ids`，整個圖文內容上傳至 Twitter 的流程到這邊就完成了。

---
## 透過 Plurk API 來發表文章
既然要透過 Plurk 來發表文章，那我們要先申請 Plurk 相關的 App Secret、Access Token ... 之類的呀！這方面的教學，我們將會在 `[EP3. 我要怎麼申請 Plurk API？好像很難又好像很簡單欸！]` 當中詳細解說。

完成了 Facebook、Twitter 串接以後，我們接下來要串接最簡單(我覺得)的社群平台了，同樣假設我們已經完成了表單、收到表單 ... 等等動作，現在要把文章發表到 Plurk 平台上了，我們首當其衝要先瀏覽 Plurk API Docs。

> 官方：[Plurk API 2.0](https://www.plurk.com/API)

老實說我覺得 Plurk 的 API Docs 非常難以閱讀，整個篇幅太過於陽春了，所以我將整份文件使用 lord/slate 來稍微美化一下，你在瀏覽上會更加方便。

> Kantai版 Plurk API Docs：[Plurk API 2.0](https://kantai235.github.io/Plurk-API-Docs/build/#plurk-api-2-0)
> lord/slate：[GitHub - lord/slate: Beautiful static documentation for your API](https://github.com/lord/slate)

我們會看到 `/APP/Timeline/plurkAdd` 這項 API，顯然這項功能就是發表一則噗文，另外還會找到 `/APP/Timeline/uploadPicture` 這項 API，功能顯然也就是將圖片上傳至噗浪，並取得圖片網址，既然我們已經找到我們需要的 API 了，那接下來我們要做的事情就是撰寫功能了。

[CODE04]
```php
/**
 * Class SocialCardsService.
 */
class SocialCardsService extends BaseService
{

	/**
	 * @param SocialCards $social_cards
	 * 
	 * @return SocialCards
	 */
	public function releasePlurk(SocialCards $social_cards) : SocialCards
	{
		// Code ...
	}
}
```
`SocialCardsService - releasePlurk`

這邊眼尖的讀者們應該會發現，前面 Facebook、Twitter 都會需要 use，為什麼這邊不用？這是因為 CQD/qlurk 本身沒有 providers、aliases，但並不代表它不好，他還是非常好用的，`至少他幫你解決了 OAuth 上的一堆麻煩事`。

> [GitHub - CQD/qlurk: A Plurk API library in PHP](https://github.com/CQD/qlurk)

[CODE05]
```php
public function releasePlurk(SocialCards $social_cards) : SocialCards {
	if ($social_cards->plurk_card_id == null) {
		try {
			$content = $social_cards->content;
			if (mb_strlen($content, "utf-8") > 220)
				$content = mb_substr($content, 0, 220, "utf-8") . " ...";
    
			$qlurk = new \Qlurk\ApiClient(
				env('PLURK_CLIENT_ID'),
				env('PLURK_CLIENT_SECRET'),
				env('PLURK_TOKEN'),
				env('PLURK_TOKEN_SECRET')
			);
			$picture = $qlurk->call('/APP/Timeline/uploadPicture', ['image'=>Storage::get('file_url')]);
			$resp = $qlurk->call('/APP/Timeline/plurkAdd', [
				'content'   => $picture['full'] . $content,
				'qualifier' => 'says',
				'lang'      => 'tr_ch'
			]);
         
			$social_cards = $this->socialCardsRepostory(...)

			return $social_cards;
		} catch(Exception $e) {
			\Log::error('...');
			return $social_cards;
		}    
	} else {
		return $social_cards;
	}
}
```
`releasePlurk(SocialCards $social_cards)`

整個步驟是這樣子，我們先透過 `mb_strlen` 來判斷內文的長度大小，如果過長就用 `mb_substr` 來做切割，接下來透過 `Storage::get` 來將圖片抓出來，並且透過 `/APP/Timeline/uploadPicture` 將圖片上傳至 Plurk，再透過 `$picture['full']` 將圖片連結抓出來，丟給 `/APP/Timeline/plurkAdd`，Plurk 是把它當作 url 嵌入文章當中，整個圖文內容上傳至 Plurk 的流程到這邊就完成了。

---

## 後續動作、衍伸做法

如果我們把每個平台都獨立寫成一個 function，並且傳入固定 Entity、回傳固定 Entity 的話，那麼我們在 Controller 去呼叫 Service 的時候就可以用很特別的做法，例如在發文的 Controller 當中，當你要觸發發表文章的 Service 的時候，你可以這樣寫：

[CODE06]
```php
/**
 * Store a newly created resource in storage.
 *
 * @param  App\Http\Requests\Frontend\Social\SocialCardsRequest $request
 * 
 * @return \Illuminate\Http\Response
 */
public function store(SocialCardsRequest $request)
{
	// Code ...

	$social_cards = $this->socialCardsService->releaseFacebook($social_cards);
	$social_cards = $this->socialCardsService->releaseTwitter($social_cards);
	$social_cards = $this->socialCardsService->releasePlurk($social_cards);

	// Code ...
}
```
`在 Controller 當中呼叫 Service 的參考做法`

Controller 只負責把這件事情委派給 Service 去處理，無論處理得如何，Controller 就只需要靜靜的等待結果、取得結果，然後針對結果來做事情，而 Service 則是負責把文章發表到社群平台當中，無論有沒有成功發表，最後一定要回傳結果給 Controller，講到這邊，是不是有點熟悉？就留給讀者慢慢思考。

如果仔細觀察的話，你會發現 Twitter、Plurk 都需要寫個判斷文章內容長度的程式碼，如果更近一步優化的話，我們可以把這段程式碼分割出來寫個 function，並且針對每個社群平台去處理不同的事項，做成一個內容產生 function。

---

## 結語

基本上我覺得程式是沒什麼問題的，真正麻煩的會是申請應用程式的這個部分，Twitter、Plurk 放得很寬，但 Facebook 可是史上無敵麻煩，你需要說明每個步驟、錄影並提交給審核人員查看，在這些部分我們將在其他篇幅來解答，不過你會遇到一個問題，如果文章量越來越大，那使用者發一篇文，我就要切換三個平台來刪文，會不會太麻煩了啊？所以接下來我們將要介紹 [P5. 純靠北工程師怎麼做的？一顆按鈕就把你的文章 BANG 不見！] 來把麻煩解決掉！

社群平台 應用程式申請：
- EP1. 我要怎麼申請 Facebook API？好像很難又好像很簡單欸！
- EP2. 我要怎麼申請 Twitter API？好像很難又好像很簡單欸！
- EP3. 我要怎麼申請 Plurk API？好像很難又好像很簡單欸！

---

## 資料參考

> - [Graph API - 文件 - Facebook for Developers](https://developers.facebook.com/docs/graph-api?locale=zh_TW)
> - [Twitter Developer Platform — Twitter      Developers](https://developer.twitter.com)
> - [Plurk API 2.0](https://www.plurk.com/API)
> - [Plurk API 2.0](https://kantai235.github.io/Plurk-API-Docs/build/#plurk-api-2-0)
> - [GitHub - SammyK/LaravelFacebookSdk: Fully unit tested Facebook SDK v5 integration for Laravel & Lumen](https://github.com/SammyK/LaravelFacebookSdk)
> - [GitHub - thujohn/twitter: Twitter API for Laravel 4/5](https://github.com/thujohn/twitter)
> - [GitHub - CQD/qlurk: A Plurk API library in PHP](https://github.com/CQD/qlurk)