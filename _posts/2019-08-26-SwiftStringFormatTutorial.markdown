---
date            : 2019-08-26 12:00:00
layout          : post
title           : 如何在 Swift 當中透過 String Format 格式來簡單處理數值內容？
subtitle        : 在開發應用程式時，我們經常會需要做數值轉換成特定格式，例如將小數點數值取兩位就好 ...
description     : "在開發應用程式時，我們經常會需要做數值轉換成特定格式，例如將小數點數值取兩位就好 ..."
image           : https://i.imgur.com/IPmKJbS.png
optimized_image : https://i.imgur.com/IPmKJbS.png
category        : tutorial
tags            :
- Swift
- Format
author          : kantai235
origin_url      : https://kantai235.github.io/SwiftStringFormatTutorial
---

在開發應用程式時，我們經常會需要做數值轉換成特定格式，例如將小數點數值取兩位就好，或者整數補 0 到十位數，諸如此類的功能，接下來我們就直接進入實際應用。

## 實際應用

#### 不做任何處理，直接將 Float 數值輸出
```swift
let floatValue: Float! = 1.23456789
let formatString: String! = String(format: "%f", floatValue)
/**
 * Output: 1.234568
 */
```

#### 將 Float 數值捨去小數點至二位數
```swift
let floatValue: Float! = 1.23456789
let formatString: String! = String(format: "%.2f", floatValue)
/**
 * Output: 1.23
 */
```

#### 將 Int 數值轉為 16 進位
```swift
let hexValue: Int! = 255
let formatString: String! = String(format: "%x", hexValue)
/**
 * Output: ff
 */
```

#### 將 Int 數值轉為 16 進位，並且補 0 到六位數
```swift
let hexValue: Int! = 255
let formatString: String! = String(format: "%06x", hexValue)
/**
 * Output: 0000ff
 */
```

#### 不做任何處理，直接將 Int 數值輸出
```swift
let intValue: Int! = 123
let formatString: String! = String(format: "%d", intValue)
/**
 * Output: 123
 */
```

#### 不做任何處理，直接將 Int 數值輸出，並且補 0 到六位數
```swift
let intValue: Int! = 123
let formatString: String! = String(format: "%06d", intValue)
/**
 * Output: 000123
 */
```

---

## 番外實驗篇

```swift
let floatValue: Float! = 3.33333333
let formatString: String! = String(format: "%f", floatValue)
/**
 * Output: 3.333333
 */

let floatValue: Float! = 4.44444444
let formatString: String! = String(format: "%f", floatValue)
/**
 * Output: 4.444445
 */
```

所以這意思是小數點第六位後會以四捨五入的方式來進位囉？這時候我產生了這個疑問，所以在 `3.33333345` 會因為四捨五入而進位成 `3.333334` 嗎？於是又做了一些額外實驗。

```swift
let floatValue: Float! = 3.33333345
let formatString: String! = String(format: "%f", floatValue)
/**
 * Output: 3.333333
 */

let floatValue: Float! = 3.33333355
let formatString: String! = String(format: "%f", floatValue)
/**
 * Output: 3.333333
 */

let floatValue: Float! = 3.33333366
let formatString: String! = String(format: "%f", floatValue)
/**
 * Output: 3.333334
 */

let floatValue: Float! = 3.33333361
let formatString: String! = String(format: "%f", floatValue)
/**
 * Output: 3.333333
 */

let floatValue: Float! = 3.33333362
let formatString: String! = String(format: "%f", floatValue)
/**
 * Output: 3.333334
 */
```

這個現象頗為奇妙的，總之先記錄起來，理論上應該無傷大雅。
