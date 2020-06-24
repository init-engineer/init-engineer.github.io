---
date            : 2019-07-17 12:00:00
layout          : post
title           : 線上編碼、解碼器 for Unicode
subtitle        : 一款簡單的線上編碼、解碼器，Unicode 是一種電腦科學領域裡的一項業界標準。它對世界上大部分的文字系統進行了整理、編碼，使得電腦可以用更為簡單的方式來呈現和處理文字。
description     : "一款簡單的線上編碼、解碼器，Unicode 是一種電腦科學領域裡的一項業界標準。它對世界上大部分的文字系統進行了整理、編碼，使得電腦可以用更為簡單的方式來呈現和處理文字。"
image           : https://i.imgur.com/htdyD9Z.png
optimized_image : https://i.imgur.com/htdyD9Z.png
category        : tools
tags            :
- Tools
- Unicode
author          : kantai235
origin_url      : https://kantai235.github.io/ToolsUnicode
---

<style type="text/css" media="screen">
    .container {
        margin: 0px auto;
        max-width: 800px;
    }

    textarea {
        margin-bottom: 0px !important;
        border-radius: 12px;
    }

    .contact-form button[type="button"] {
        display: inline;
        padding: 19px 39px 18px 39px;
        color: #fff;
        font-size: 1.125rem;
        width: 49%;
        border: 1px solid #ba0009;
            border-top-width: 1px;
            border-right-width: 1px;
            border-bottom-width: 1px;
            border-left-width: 1px;
        /* margin: 0px auto; */
        margin-top: .625rem;
        margin-bottom: .625rem;
        cursor: pointer;
        -webkit-transition: all .3s;
        transition: all .3s;
        outline: none;
    }

    select {
        padding: 9px 18px 9px 18px;
        width: 100%;
        /* border: 6px solid #032629 !important; */
        /* border-width: 2px 4px 4px 2px !important; */
        border-radius: 12px;
        margin: .625rem;
        padding: .625rem;
    }

    .contact-form .encode {
        background: #086770;
        border: 6px solid #032629 !important;
        border-width: 2px 4px 4px 2px !important;
    }

    .contact-form .decode {
        background: #427035;
        border: 6px solid #243e1d !important;
        border-width: 2px 4px 4px 2px !important;
    }

    .encode_textarea {
        border: 6px solid #086770 !important;
            border-top-width: 1px;
            border-right-width: 1px;
            border-bottom-width: 1px;
            border-left-width: 1px;
    }

    .decode_textarea {
        border: 6px solid #427035 !important;
        border-top-width: 1px;
        border-right-width: 1px;
        border-bottom-width: 1px;
        border-left-width: 1px;
    }
</style>

<div class="container">
    <div id="form" class="contact-form">
        <textarea type="text" class="encode_textarea" id="encode_value" name="encode_value" placeholder="在此輸入您想要「編碼」為 URL 的文本內容 ..."></textarea>
        <button type="button" class="encode" id="encode" onclick="encode()">編碼 Encode</button>
        <button type="button" class="decode" id="decode" onclick="decode()">解碼 Decode</button>
        <textarea type="text" class="decode_textarea" id="decode_value" name="decode_value" placeholder="在此輸入您想要「解碼」為文本的 URL 內容 ..."></textarea>
    </div>
</div>

<script>
    function encode() {
        var sMyInput = $('#encode_value').val();
        var sMyUnicode = sMyInput.toUnicode();
        $('#decode_value').val(sMyUnicode);
    };

    function decode() {
        var sMyUnicode = $('#decode_value').val();
        var r = /\\u([\d\w]{4})/gi;
        sMyUnicode = sMyUnicode.replace(r, function (match, grp) {
            return String.fromCharCode(parseInt(grp, 16));
        } );
        sMyUnicode = unescape(sMyUnicode);
        $('#encode_value').val(sMyUnicode);
    };

    String.prototype.toUnicode = function() {
        var result = "";
        for(var i = 0; i < this.length; i++) {
            // Assumption: all characters are < 0xffff
            result += "\\u" + ("000" + this[i].charCodeAt(0).toString(16)).substr(-4);
        }
        return result;
    };
</script>

> # Unicode
> Unicode（中文：萬國碼、國際碼、統一碼、單一碼）是電腦科學領域裡的一項業界標準。它對世界上大部分的文字系統進行了整理、編碼，使得電腦可以用更為簡單的方式來呈現和處理文字。
> Unicode 伴隨著通用字元集的標準而發展，同時也以書本的形式對外發表。Unicode 至今仍在不斷增修，每個新版本都加入更多新的字元。目前最新的版本為 2019 年 5 月公布的 12.1.0，已經收錄超過13萬個字元（第十萬個字元在 2005 年獲採納）。Unicode 涵蓋的資料除了視覺上的字形、編碼方法、標準的字元編碼外，還包含了字元特性，如大小寫字母。
> Unicode 發展由非營利機構統一碼聯盟負責，該機構致力於讓 Unicode 方案取代既有的字元編碼方案。因為既有的方案往往空間非常有限，亦不適用於多語環境。
> Unicode 備受認可，並廣泛地應用於電腦軟體的國際化與在地化過程。有很多新科技，如可延伸標示語言（Extensible Markup Language，簡稱：XML）、Java 程式語言以及現代的作業系統，都採用 Unicode 編碼。
> - 資料來源：[統一資源定位符 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/url)
