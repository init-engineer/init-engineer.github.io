---
date            : 2019-03-17 12:00:00
layout          : post
title           : P5. 純靠北工程師怎麼做的？一顆按鈕就把你的文章 BANG 不見！
subtitle        : 既然是社群匿名發文平台，那主要核心當然是發廢文、講幹話啦！可是這要怎麼做呢？文章管理三步驟：繪圖、發表文章、刪除文章 ...
description     : "既然是社群匿名發文平台，那主要核心當然是發廢文、講幹話啦！可是這要怎麼做呢？文章管理三步驟：繪圖、發表文章、刪除文章 ..."
image           : https://i.imgur.com/OmixAz5.png
optimized_image : https://i.imgur.com/OmixAz5.png
category        : tutorial
tags            :
- Laravel
author          : kantai235
origin_url      : https://kantai235.github.io/P5HowDoYouRelyOnTheKaobeiEngineer
---

既然是社群匿名發文平台，那主要核心當然是發廢文、講幹話啦！可是這要怎麼做呢？文章管理三步驟：繪圖、發表文章、刪除文章。

- P3. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(1/2)
- P4. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(2/2)
- `P5. 純靠北工程師怎麼做的？一顆按鈕就把你的文章 BANG 不見！ (本篇)`
- P6. 純靠北工程師怎麼做的？自幹前端一個繪圖、後端一個繪圖！前端篇
- P7. 純靠北工程師怎麼做的？自幹前端一個繪圖、後端一個繪圖！後端篇

---

## 簡介

當我們完成社群平台發表文章之後，如果你只有在管理一個社群平台，那很輕鬆、很簡單，看到廢文直接手動刪掉就好了！可是如果今天你有兩個 Facebook 粉絲團要管理，以及 Twitter、Plurk 需要管理，使用者發表一篇廢文，你總共有四個社群平台帳號要切換，你可能就會覺得真心很累，所以接下來我們要做的就是把刪除文章這件事情給簡單化，當然整篇文章會聚焦在 API 的應用當中。

- Facebook API - delete/{post_id} 串接
- Twitter API - destroyTweet 串接
- Plurk API - plurkDelete 串接

接下來我們會透過一些第三方 Library 來完成我們串接 API 的事情，安裝 Library 的方法可以回顧一下 `[P3. 純靠北工程師怎麼做的？一顆按鈕就把你的文章發到世界各地了呢！(1/2)]` 當中有提到。

> - [GitHub - SammyK/LaravelFacebookSdk: Fully unit tested Facebook SDK v5 integration for Laravel & Lumen](https://github.com/SammyK/LaravelFacebookSdk)
> - [GitHub - thujohn/twitter: Twitter API for Laravel 4/5](https://github.com/thujohn/twitter)
> - [GitHub - CQD/qlurk: A Plurk API library in PHP](https://github.com/CQD/qlurk)

---

## 設計流程

在寫程式之前，我們總是要先來思考一下流程，以確保整個設計思維是對的，如果你的軟體有設計 Backend、Frontend 的話，那麼很顯然發表文章的功能是泛用於 Backend 與 Frontend，而刪除文章的這項功能則是真真切切的只存在於 Backend，接下來就是流程的問題，假設管理員現在找到一篇廢文需要刪掉，所以管理員按下刪除按鈕之後，系統就要準備進行刪除文章的流程，首先逐一平台去將文章刪除，無論刪除成功與否，都要儲存文章狀態(是否被刪除)，每個平台刪除完了以後，再透過軟刪除的方式將文章移除，這篇文章已經進入垃圾桶了，最後將網頁重新導向到已刪除文章的列表當中。

![https://i.imgur.com/beQWeR6.png](https://i.imgur.com/beQWeR6.png)
構思整個刪除文章的流程

眼尖的讀者應該會發現說，這邊流程上的設計跟發表文章有點像，刪除文章的動作無論失敗、成功，最後都會導向下一個動作，其實像這種針對同一個物件不斷處理事情，都可以用這種方式來撰寫。

```php
public function destroy(SocialCardsRequest $request)
{
	// Code ...

	$social_cards = $this->socialCardsService->deletedFacebook($social_cards);
	$social_cards = $this->socialCardsService->deletedTwitter($social_cards);
	$social_cards = $this->socialCardsService->deletedPlurk($social_cards);

	// Code ...
}
```
在 Controller 當中呼叫 Service 的參考做法

---

## 透過 Facebook API 來刪除文章

既然要透過 Facebook 來刪除文章，那我們要先申請 Facebook 相關的 App Secret、Access Token ... 之類的呀！這方面的教學，我們將會在 `[EP1. 我要怎麼申請 Facebook API？好像很難又好像很簡單欸！]` 當中詳細解說。

完成前置步驟之後，假設我們已經要準備進行刪除文章的動作了，現在要把指定文章從 Facebook 平台當中移除，所以可以打開 API 文件來看一下。

> - [Graph API - 文件 - Facebook for Developers](https://developers.facebook.com/docs/graph-api?locale=zh_TW)

根據我們之前發表文章的範例，發表文章是透過 `/Post/post-id - Publishing` 以及 `/{page_id}/photos - Creating` 來完成：

> - [貼文 - Graph API - 文件 - Facebook for Developers](https://developers.facebook.com/docs/graph-api/reference/v3.2/post#publishing)
> - [Graph API Photo Node - Documentation - Facebook for Developers](https://developers.facebook.com/docs/graph-api/reference/photo/#Creating)

那麼也就是說，我們可以透過 `/Post/post-id - deleting` 以及 `/{page_id}/photos - deleting` 來完成刪除文章的動作囉？沒錯，這個思維是正確的：

> - [貼文 - Graph API - 文件 - Facebook for Developers](https://developers.facebook.com/docs/graph-api/reference/v3.2/post#deleting)
> - [Graph API Photo Node - Documentation - Facebook for Developers](https://developers.facebook.com/docs/graph-api/reference/photo/#Deleting)

既然我們已經知道我們要使用的 API 是什麼了，那麼我們接下來就可以直接寫功能了，不外乎是寫在 Service 當中，可能會有一個 deletedFacebook 的方法，其功能是負責把特定社群平台的指定文章給刪除。

```php
use Facebook;

/**
 * Class SocialCardsService.
 */
class SocialCardsService extends BaseRepository
{
	/**
 	 * @param SocialCards $social_cards
 	 * 
 	 * @return SocialCards
 	 */
	public function deletedFacebook(SocialCards $social_cards) : SocialCards
	{
		// Code ...
	}
}
```
SocialCardsService - deletedFacebook

在這邊我們要透過 `Facebook::delete ` 去刪除指定文章，只要在 `API URL ` 帶入 `post_id `，不需要帶任何參數，就可以將文章刪除，刪除後會有個 response 告訴你有沒有成功、詳細資訊之類的，我們要再寫個方法確認一下是否成功，如果有例外就直接 `throw exception`，如果一切都順利的話，就呼叫 Repostory 去把資料紀錄一下，結束這個回合。

```php
/**
 * @param SocialCards $social_cards
 * 
 * @return SocialCards
 */
public function deletedFacebook(SocialCards $social_cards) : SocialCards {
	if ($social_cards->facebook_card_active) {
		try {
			Facebook::setDefaultAccessToken(env('FACEBOOK_USER_ACCESS_TOKEN'));
			$response = Facebook::delete('/' . $social_cards->facebook_card_id, array());

			if ($this->checkDeletedResponse($response))
				throw new GeneralException(...);

			$social_cards = $this->socialCardsRepostory(...)

			return $social_cards;
		} catch(\Facebook\Exceptions\FacebookSDKException $e) {
			\Log::error('...');
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
deletedFacebook(SocialCards $social_cards)

---

## 透過 Twitter API 來刪除文章

既然要透過 Twitter 來刪除文章，那我們要先申請 Twitter 相關的 App Secret、Access Token ... 之類的呀！這方面的教學，我們將會在 `[EP2. 我要怎麼申請 Twitter API？好像很難又好像很簡單欸！]` 當中詳細解說。

完成前置步驟之後，假設我們已經要準備進行刪除文章的動作了，現在要把指定文章從 Twitter 平台當中移除，所以可以打開 API 文件來看一下。

> - [Twitter Developer Platform — Twitter      Developers](https://developer.twitter.com)

根據我們之前發表文章的範例，發表文章是透過 `statuses/update ` 來完成，換句話說刪除文章的功能應該也在 `statuses/* ` 當中的某一支，仔細找一下會發現 `statuses/destroy/:id ` 這項 API 就是我們需要的。

> Destroys the status specified by the required ID parameter. The authenticating user must be the author of the specified status. Returns the destroyed status if successful.
> - [POST statuses/destroy/:id — Twitter      Developers](https://developer.twitter.com/en/docs/tweets/post-and-engage/api-reference/post-statuses-destroy-id)

我們再反向去找 `thujohn/twitter`，會找到 `destroyTweet($id, $parameters = [])` 這個方法，正是我們要使用的目標。

```php
/**
 * Destroys the status specified by the required ID parameter. The authenticating user must be the author of the specified status. Returns the destroyed status if successful.
 *
 * Parameters :
 * - trim_user (0|1)
 */
	public function destroyTweet($id, $parameters = [])
	{
		return $this->post('statuses/destroy/'.$id, $parameters);
	}
```
destroyTweet($id, $parameters = [])

既然已經找到我們需要用的方法了，那接下來就是開始動工，一樣會在 Service 當中撰寫功能，會有個 deletedTwitter 來寫刪除 Twitter 文章相關的功能。

```php
use Twitter;

/**
 * Class SocialCardsService.
 */
class SocialCardsService extends BaseRepository
{
	/**
 	 * @param SocialCards $social_cards
 	 * 
 	 * @return SocialCards
 	 */
	public function deletedTwitter(SocialCards $social_cards) : SocialCards
	{
		// Code ...
	}
}
```
SocialCardsService - deletedTwitter

在這邊我們要透過 `Twitter::destroyTweet` 去刪除指定文章，只要帶入 `post_id`，就可以將文章刪除，刪除後會有個 response 告訴你有沒有成功、詳細資訊之類的，我們要再寫個方法確認一下是否成功，如果有例外就直接 `throw exception`，如果一切都順利的話，就呼叫 Repostory 去把資料紀錄一下，結束這個回合。

```php
/**
 * @param SocialCards $social_cards
 * 
 * @return SocialCards
 */
public function deletedTwitter(SocialCards $social_cards) : SocialCards {
	if ($social_cards->twitter_card_active) {
		try {
			$response = Twitter::destroyTweet($social_cards->twitter_card_id);

			if ($this->checkDeletedResponse($response))
				throw new GeneralException(...);

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
deletedTwitter(SocialCards $social_cards)

---

## 透過 Plurk API 來刪除文章

最後我們來到 Plurk 刪除文章的部分，既然要透過 Plurk 來刪除文章，那我們要先申請 Plurk 相關的 App Secret、Access Token ... 之類的呀！這方面的教學，我們將會在 `[EP3. 我要怎麼申請 Plurk API？好像很難又好像很簡單欸！]` 當中詳細解說。

完成前置步驟之後，假設我們已經要準備進行刪除文章的動作了，現在要把指定文章從 Plurk 平台當中移除，所以可以打開 API 文件來看一下。

> - 官方：[Plurk API 2.0](https://www.plurk.com/API)

再來安麗一下，老實說我覺得 Plurk 的 API Docs 非常難以閱讀，整個篇幅太過於陽春了，所以我將整份文件使用 lord/slate 來稍微美化一下，你在瀏覽上會更加方便。

> - Kantai版 Plurk API Docs：[Plurk API 2.0](https://kantai235.github.io/Plurk-API-Docs/build/#plurk-api-2-0)
lord/slate：[GitHub - lord/slate: Beautiful static documentation for your API](https://github.com/lord/slate)

回到正題，我們會看到 `/APP/Timeline/plurkDelete` 這項 API，顯然這項功能就是刪除指定的一則噗文，既然我們已經找到我們需要的 API 了，那接下來我們要做的事情就是撰寫功能了。

```php
/**
 * Class SocialCardsService.
 */
class SocialCardsService extends BaseRepository
{
	/**
 	 * @param SocialCards $social_cards
 	 * 
 	 * @return SocialCards
 	 */
	public function deletedPlurk(SocialCards $social_cards) : SocialCards
	{
		// Code ...
	}
}
```
SocialCardsService - deletedPlurk

在這邊我們要透過 `/APP/Timeline/plurkDelete` 去刪除指定文章，只要帶入 `plurk_id`，就可以將文章刪除，這邊需要注意的是 `plurk_id` 指的是 10 進位的，而瀏覽噗文的網址則是 36 進位，刪除後會有個 response 告訴你有沒有成功、詳細資訊之類的，我們要再寫個方法確認一下是否成功，如果有例外就直接 `throw exception`，如果一切都順利的話，就呼叫 Repostory 去把資料紀錄一下，結束這個回合。

```php
public function deletedPlurk(SocialCards $social_cards) : SocialCards {
	if ($social_cards->plurk_card_active) {
		try {
			$qlurk = new \Qlurk\ApiClient(
				env('PLURK_CLIENT_ID'),
				env('PLURK_CLIENT_SECRET'),
				env('PLURK_TOKEN'),
				env('PLURK_TOKEN_SECRET')
			);
			$response = $qlurk->call('/APP/Timeline/plurkDelete', [
				'plurk_id' => base_convert($social_cards->plurk_card_id, 36, 10)
			]);

			if ($this->checkDeletedResponse($response))
				throw new GeneralException(...);

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
deletedPlurk(SocialCards $social_cards)

---

## 結語

基本上發表文章、刪除文章你都做完了，你不只做完核心功能了，管理上還很方便，只需要按一個按鈕就可以批次把所有社群平台的文章刪除，基本上整個純靠北工程師刪除文章的方法就是這樣，實際走一次看看：

![https://i.imgur.com/6rzXUAZ.png](https://i.imgur.com/6rzXUAZ.png)
文章列表 - 全部的文章

![https://i.imgur.com/q5zODHT.png](https://i.imgur.com/q5zODHT.png)
點選要刪除的文章

![https://i.imgur.com/lSIUilG.png](https://i.imgur.com/lSIUilG.png)
確認刪除文章

![https://i.imgur.com/MQxRSQ4.png](https://i.imgur.com/MQxRSQ4.png)
文章列表 - 已刪除的文章

除此之外，最會遇到問題的應該就是社群平台的應用程式申請，在這邊獨立開 EP 章節來帶大家走一次申請流程、介紹相關工具的應用。

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