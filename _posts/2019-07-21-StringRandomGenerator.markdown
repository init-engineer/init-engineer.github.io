---
date            : 2019-07-21 12:00:00
layout          : post
title           : 線上 String 字串亂數產生器
subtitle        : 一款簡單的 String 字串線上產生器，可用來產生自定義的長度、大寫小寫英文、數字、字元符號 ... 通通自定義。
description     : "一款簡單的 String 字串線上產生器，可用來產生自定義的長度、大寫小寫英文、數字、字元符號 ... 通通自定義。"
image           : https://i.imgur.com/tLzYHX5.png
optimized_image : https://i.imgur.com/tLzYHX5.png
category        : generator
tags            :
- String
- Random
- Generator
author          : kantai235
origin_url      : https://kantai235.github.io/StringRandomGenerator
---

<style>
    /* The switch - the box around the slider */
    .switch {
        position: relative;
        display: inline-block;
        width: 60px;
        height: 34px;
    }

    /* Hide default HTML checkbox */
    .switch input {
        opacity: 0;
        width: 0;
        height: 0;
    }

    /* The slider */
    .slider {
        position: absolute;
        cursor: pointer;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: #ccc;
        -webkit-transition: .4s;
        transition: .4s;
    }

    .slider:before {
        position: absolute;
        content: "";
        height: 26px;
        width: 26px;
        left: 4px;
        bottom: 4px;
        background-color: white;
        -webkit-transition: .4s;
        transition: .4s;
    }

    input:checked + .slider {
        background-color: #2196F3;
    }

    input:focus + .slider {
        box-shadow: 0 0 1px #2196F3;
    }

    input:checked + .slider:before {
        -webkit-transform: translateX(26px);
        -ms-transform: translateX(26px);
        transform: translateX(26px);
    }

    /* Rounded sliders */
    .slider.round {
        border-radius: 34px;
    }

    .slider.round:before {
        border-radius: 50%;
    }

    .decode_textarea {
        border: 6px solid #427035 !important;
        border-top-width: 1px;
        border-right-width: 1px;
        border-bottom-width: 1px;
        border-left-width: 1px;
    }
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
        width: 100%;
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

    .contact-form .generate {
        background: #086770;
        border: 6px solid #032629 !important;
        border-width: 2px 4px 4px 2px !important;
    }

    .generate_number {
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
        outline: none;
        display: block;
        color: #333;
        width: 100%;
        padding: 7px;
        border: none;
            border-bottom-color: currentcolor;
            border-bottom-style: none;
            border-bottom-width: medium;
        border-bottom: 1px solid #ddd;
        margin-bottom: 10px;
        font-family: inherit;
        font-size: 1.125rem;
        height: 2.813rem;
    }
</style>

<!-- Rectangular switch -->
<label class="switch">
    <input type="checkbox" id="number_checked" checked>
    <span class="slider round"></span>
</label>
亂數當中包含「數字（0123456789）」

<!-- Rounded switch -->
<label class="switch">
    <input type="checkbox" id="capital_checked" checked>
    <span class="slider round"></span>
</label>
亂數當中包含「大寫英文（ABCDEFGHIJKLMNOPQRSTUVWXTZ）」

<!-- Rounded switch -->
<label class="switch">
    <input type="checkbox" id="small_checked" checked>
    <span class="slider round"></span>
</label>
亂數當中包含「小寫英文（abcdefghijklmnopqrstuvwxyz）」

<!-- Rounded switch -->
<label class="switch">
    <input type="checkbox" id="number_index">
    <span class="slider round"></span>
</label>
亂數的開頭加個數次（e.g.: 1. 2. 3. ...）

<div class="container">
    <div id="form" class="contact-form">
        亂數產生長度
        <input type="number" class="generate_number" id="generate_number_1" name="generate_number_1" value="64">
        亂數產生數量
        <input type="number" class="generate_number" id="generate_number_2" name="generate_number_2" value="1">
        <button type="button" class="generate" id="generate" onclick="generate()">產生亂數</button>
        <textarea type="text" class="generate_textarea" id="generate_value" name="generate_value" placeholder="這裡會生出亂數。"></textarea>
    </div>
</div>

<script>
    // $('#aaa').prop('checked');

    function generate_random_string(string_length, ascii_low, ascii_high) {
        let random_string = '';
        let random_ascii;
        for (let i = 0; i < string_length; i++) {
            random_ascii = Math.floor((Math.random() * (ascii_high - ascii_low)) + ascii_low);
            random_string += String.fromCharCode(random_ascii)
        }
        return random_string;
    }

    function generate_random_number() {
        let num_low = 1;
        let num_high = 9;
        return Math.floor((Math.random() * (num_high - num_low)) + num_low);
    }

    function randomString() {
        var number_checked  = $('#number_checked').prop('checked'),
            capital_checked = $('#capital_checked').prop('checked'),
            small_checked   = $('#small_checked').prop('checked');
        var number_chars  = '0123456789',
            capital_chars = 'ABCDEFGHIJKLMNOPQRSTUVWXTZ',
            small_chars   = 'abcdefghijklmnopqrstuvwxyz',
            chars         = '';

        if (number_checked) { chars += number_chars; }
        if (capital_checked) { chars += capital_chars; }
        if (small_checked) { chars += small_chars; }

        if (chars == '') {
            return null;
        }

        var randomstring = '';
        for (var i = 0; i < $('#generate_number_1').val(); i++) {
            var rnum = Math.floor(Math.random() * chars.length);
            randomstring += chars.substring(rnum, rnum + 1);
        }

        return randomstring;
    }

    function generate() {
        var result = '';
        for (var i = 1; i <= $('#generate_number_2').val(); i++) {

            if ($('#number_index').prop('checked')) {
                result += i + '. ' + randomString() + '\n';
            } else {
                result += randomString() + '\n';
            }
        }

        $('#generate_value').val(result);
    }
</script>
