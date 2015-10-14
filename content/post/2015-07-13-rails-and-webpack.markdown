+++
date = "2015-07-13T22:24:44+08:00"
draft = false
title = "用 webpack 取代 Rails Asset Pipeline"

+++

Rails 的 Asset Pipeline 十分好用, 但是跟 webpack 比起來還是有不小的落差

所以研究了一下把 Rails 前端的部份整個換成 webpack 的方法

希望達到的目標有

1. 原本在 app/assets 下的東西還是放在下面
1. 原本使用的 helper 名稱繼續沿用
1. 建立出來的檔案有打上 digest
1. 特定的 third party js 跟 images 不需要透過 require 處理, 只需直接將檔名打上 digest

調整的設定檔大致上就是 https://gist.github.com/tka/45f2923236ad615dcdd5

原理是

1. webpack 輸出檔案到 /public/assets 下
1. 透過 static_resources.js 把 third party js 跟 image 都 requie 進來, 然後用 file-loader 輸出成檔案
1. webpack 跑完之後讓他吐一個 manifest.json 然後改寫 rails helper 改成從 manifest.json 內拿到編譯完的檔案名稱

如此初步看起來就可以滿足需求了, 下個專案有機會的話就來實戰看看
