---
layout: post
title: "設定YARD環境"
date: "2013-08-21T03:13:00+08:00"
comments: true
categories: 
---



添加專案中的 `Gemfile` 與 `.yardopts` 如下

Gemfile 

```ruby
group :development do
  gem 'guard'
  gem 'guard-yard'
  gem 'redcarpet'
  gem 'github-markup'
end
```

.yardopts
```ruby 
--markup-provider=redcarpet
--markup=markdown
```

接下來執行 `guard init yard` 初始化 guard 的環境,
之後只需要執行 `guard start` 便可監控檔案異動, 立刻透過 YARD 產生文件
