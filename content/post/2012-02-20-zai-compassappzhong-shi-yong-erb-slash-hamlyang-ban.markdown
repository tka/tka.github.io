---
layout: post
title: "在 <del>Compass.app</del> Fire.app 中使用ERB/Haml樣板語言"
date: "2012-02-20T22:02:00+08:00"
comments: true
categories: [Ruby]
---

## 2012-04-17 更新 
  文中提到的 Compass.app, 正式發行時改名為 [Fire.app](http://fireapp.handlino.com)

## 簡介
  
[Coompass.app](http://compass.handlino.com/)將要釋出新版本支援 ERB/Haml 樣板, 過去使用 Compass.app 雖然提供了簡單的 web server, 但是做網站 prototype 時, 總是要一頁一頁 html 用手工刻. 但是現在不用了因為即將更新的 Compass.app 會支援 ERB 跟 Haml 兩種樣板語言

## 改用 ERB 或 Haml 樣板語言的好處

#### 可以套用 layout 跟使用 partial

   網站的 Header 與 Footer 常常是重複的, 當老闆說網站的標題要改掉時, 原本你需要改100個頁面, 現在你只需要改一個檔案就可以了, 或是寫過的 sidebar , 寫新的一頁時再也不用整段 html 複製貼上了.

#### 可以使用 helper

  導覽列上目前頁面的連結, 總是要顯示出跟其他連結不同的樣式, 像是要在 class 上加上 current, 來做出特別的效果, 搭配 helper 你再也不用一頁一頁修改, 現在是哪個連結是當前頁面的連結了

  要插入一段沒有意義文字, 讓版面看起來比較像真的網站. 過去可以使用 [MoreText](http://more.handlino.com/) 用 javascript 插入假文, 現在可以讓 helper 直接把 MoreText 的假文插入網頁裡, 開發更加方便！
<!-- more -->

#### 可以使用 Ruby 語法
  
  如果願意的話, 還可學一點 Ruby 程式語言的語法, 如此再寫網頁時便可以快速的完成工作, 例如要列出 10 筆文章的標題只要像下面這樣寫就可以了

```
  <ul>
    <% (1..10).each do |x|%>
      <li>文章<%=x%> 標題</li>
    <% end %>
  </ul>
```
就會得到下面的html
``` 
<ul>
  <li>文章 1 標題</li>
  <li>文章 2 標題</li>
  <li>文章 3 標題</li>
  <li>文章 4 標題</li>
  <li>文章 5 標題</li>
  <li>文章 6 標題</li>
  <li>文章 7 標題</li>
  <li>文章 8 標題</li>
  <li>文章 9 標題</li>
  <li>文章 10 標題</li>
</ul>
```
### 樣板語法相關資料

接下來只會提到在 Compass.app 環境中使用 ERB 或是 Haml 的使用方式, 而這兩種樣板語言的語法將不會被提到, 所以建議先參考下列幾個網址先學會 ERB 跟 Haml 的語法

* ERB 
    1. http://ruby-doc.org/stdlib-1.9.3/libdoc/erb/rdoc/ERB.html
    1. http://rrn.dk/rubys-erb-templating-system
    1. http://www.stuartellis.eu/articles/erb/
* Haml
    1. http://haml-lang.com/
    1. http://blog.meituo.net/2011/03/27/haml-%E4%BD%BF%E7%94%A8%E4%BB%8B%E7%BB%8D/

### 搭配 Compass.app 使用方式

#### 建立頁面

把檔案放在  專案目錄下, 檔名用 `.html.erb` 結尾的會使用 ERB 樣板語言, 用 `.html.haml` 結為尾的會使用 Haml 樣板語言處理, 而對對應的網址就是 `http://127.0.0.1:24680/ + 檔名` 去掉結尾的 `.erb` 或 `.haml` 

例如:
  專案目錄下面建立一個檔案, 命名為 `home.html.erb`, 寫入一些內容. 然後從瀏覽器中開啟 `http://127.0.0.1:24680/home.html` 就可以看到剛剛建立的網頁了. 同理如果是放在 Compass 專案目錄下面的 `about` 目錄的 `company.html.erb` 跟 `contact.html.haml`, 網址就會是 `http://127.0.0.1:24680/about/company.html` 跟 `http://127.0.0.1:24680/about/contact.html`

#### 套用 layout

頁面有兩種方法可以套用 layout

1. 指定頁面 layout 檔案
1. 頁面同目錄或是上層目錄有 _layout.html.erb

##### 頁面同目錄或是上層目錄有 \_layout.html.erb

如果頁面沒有指定目錄, 而且頁面所在目錄或是上層目錄中有檔案被命名為`_layout.html.erb`, 就會使用該檔案作為 layout 檔案 ._

例如: 
有 `/_layout.html.erb` 存在且 `/about/_layout.html.erb` 不存在, 那 `/about/me.html.erb` 跟 `/about/contact.html.erb` 都會用 `/_layout.html.erb` 當作 layout 檔案

##### 指定頁面 layout 檔案

建立一個新檔案, 命名方式是將頁面檔案的檔名結尾從 `.html.erb` 或是 `.html.haml` 改成 `.html.layout`, 同時在該檔案裏面指定要當作 layout 的檔案名增

例如: 
`/about/me.html.erb` 要指定使用 `/about/mylayout.html.erb` 當作 layout 檔案, 就要建立新檔案並且命名為 `/about/me.html.layout`, 然後在 `/about/me.html.layout` 裏面寫 `/about/mylayout.html.erb`, 這樣就會使用 `/about/mylayout.html.erb` 來當作 layout 檔案而不是 `_layout.html.erb` 了


layout 檔案內容會長的像是下面的樣子, 其中 `<%= yield%>` 位置就是頁面會被載入的地方
``` 
<!DOCTYPE html>
<!--[if lt IE 7]> <html lang="en" class="no-js ie6 oldie"> <![endif]-->
<!--[if IE 7]>    <html lang="en" class="no-js ie7 oldie"> <![endif]-->
<!--[if IE 8]>    <html lang="en" class="no-js ie8 oldie"> <![endif]-->
<!--[if gt IE 8]><!-->
  <html class='no-js' lang='en'>
    <!--<![endif]-->
    <head>
        <meta charset='utf-8' />
        <meta content='IE=edge,chrome=1' http-equiv='X-UA-Compatible' />
        <title>Site Title</title>
        <meta content='' name='description' />
        <meta content='' name='author' />
        <meta content='width=device-width, initial-scale=1.0' name='viewport' />
        <link href='/stylesheets/style.css"%>' rel='stylesheet' />
        <script src='/javascripts/modernizr-2.5.3.js'></script>
    </head>
    <body>
        <div id='container'>
            <header></header>
            <div id='main' role='main'>
                <%= yield%>
            </div>

            <div id="root-footer"></div>
        </div>
        <div id="footer"></div>
    </body>
</html>
```
### 套用 partial

partial 是只網頁片段內容, 只要在建立檔案時用底線`_`開頭就可以當作 partial 呼叫, ex. `_top_nav.html.erb`
呼叫的時候只需要在頁面裏面寫 `<%= render("top_nav")%>`就可以了, 這邊不需要`_`跟`.html.erb`歐, 不管是頁面, layout 還是 partial 都可以使用 partial 來載入常用的內容


### 套用 helper 

編寫頁面時可以透過 `<%= helper名稱(helper參數1, helper參數2..)%>` 的方式來呼叫 helper, 所以其實套用 partial 時也是透過呼叫叫作 `render` 的 helper 來達成載入 partial 檔案內容的工作

目前 Compass.app 內建的 helper 可以參考 [Serve View Helpers](http://get-serve.com/documentation/view-helpers), 如果要定義自己的 helper, 需要 Ruby 語言的基礎, 方法是在 Compass 專案目錄中建立 `view_helpers.rb`　內容如下

``` 
module ViewHelpers
  def helper_name(arg1, arg2, ....)
      return "something"
  end
end
```

如果要做到前面提到的當前頁面標示跟載入 MoreText 可以下載[檔案](https://gist.github.com/970ce5226b95e2ec52dd)放到  專案目錄中, 如此 link_to helper 會在當前網頁路徑(ex. `/about/me.html`)相同的連結中加上 `class="current"`, moretext  helper 則可插入 MoreText 資料

