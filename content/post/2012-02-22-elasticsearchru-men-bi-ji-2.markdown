---
layout: post
date: "2012-02-26T04:16:00+08:00"
title: "elasticsearch入門筆記(2)"
comments: true
categories: elasticsearch
---

## field 種類

來源: http://www.elasticsearch.org/guide/reference/mapping/core-types.html

主要的資料格式摘要如下, 其他還有不少種類的資料格式可用.
其他詳細的內容需要參考, 該頁面右邊 Types 下面的分類(這裡的Types是指欄位的種類 )
<table>
  <tr>
    <td>string</td>
  </tr>
  <tr>
    <td>float, double, byte, short, integer, long</td>
  </tr>
  <tr>
    <td>boolean</td>
  </tr>
  <tr>
    <td>data</td>
  </tr>
  <tr>
    <td>binary</td>
  </tr>
</table>
<!-- more -->

## multi\_field 欄位格式

在許多field 種類中要特別提到的是multi\_field, multi\_field 可以做到的功能是u在建立 document 時, 將特定欄位給予多個 analyer(負責分詞的角色) 如此便可滿足中英文混雜的文章搜尋需求。

例如 `elasticsearch遠比上Google搜尋更有效率` 需要在下列幾種輸入時都會被搜尋到

1. `elast`或是`elasticsearch`: 只輸入了單字的前幾個字, 或是完整單字
1. `更有效率` 中文句子

這時候就可以將欄位設定成 multi\_field, 並且設定兩個不同的分詞方式 `cjk` 與 `edgeNGram`, 其中 `cjk` 會對中文的內容用 `nGram` 進行切詞, 而 `edgeNGram` 則會建立 `e`, `el`, `ela`, `elas`, `elast`.... 的切詞

另外要注意的事情是 multi\_field 裏面建立的欄位並不包括在預設的搜尋欄位`_all`中, 必須要特別指定才會被搜尋到
