---
layout: post
title: "Java7 + JRuby 1.7.0.preview2 + Rawr"
date: "2012-08-24T22:16:00+08:00"
comments: true
categories: jruby fireapp
---

這兩天試著把 [Fire.app](http://fireapp.handlino.com/) 升級到 [JRuby](http://jruby.org/) 1.7.0.preview2, 過程中遇到一些 ruby 1.8.7 升級到 1.9.3 的小問題, 不過解決起來還算順手。後來同事想試試看搭配 Java7 的效果如何這時候就遇到問題了。

原因在於之前打包是使用 [Rawr](https://github.com/rawr/rawr), 但是他使用的 [launch4j](http://launch4j.sourceforge.net/) 在 OS X 上面搭配 Java7 打包出來的程式無法順利執行。 看起來是因為　Java7 不是 apple 放出來的版本造成的。所以必須要改用 [Java Application Bundler](http://java.net/projects/appbundler), 這邊最簡單的方法就是一樣用 Rawr 建立 jar 檔, 然後改寫　rawr:bundle:app 這個 rake task 就好了。

主要需要處理的部份有

1. 移動程式目錄裡面的 `Contents/Resources/Java` 到 `Contents/Java`
1. 複製 Java Application Bundler 專案中的 `JavaAppLauncher` 這個檔案到 `Contents/MacOS` 目錄下
1. 改寫 `Contents/Info.plist` 的部份內容如下, 這邊的細節可以參考 Java Application Bundler 的文件跟 source

``` xml
    <key>CFBundleExecutable</key>
    <string>JavaAppLauncher</string>
    <key>JVMMainClassName</key>
    <string>com.handlino.fireapp.Main</string>
    <key>JVMOptions</key>
    <array>
      <key>-XstartOnFirstThread</key> 
    </array>

```

做好這些步驟之後, 就可以像過去一樣輕鬆的打包出各個平台使用 Java7 + JRuby 1.7.0.preview2 程式
