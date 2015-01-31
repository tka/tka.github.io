---
layout: post
title: 將 Rails 專案中使用的 MySQL 轉移到 PostgreSQL
date: "2012-04-16T15:13:00+08:00"
comments: true
categories: 
---


1. 開一個新的 Rails 專案, DB 使用 PostgreSQL
1. 將舊專案中的 db/schema.rb 改寫成新專案中的 db migration
1. 在新專案中跑 `rake db:migrate` 建立 PostgreSQL 中的資料庫
1. 使用 [mysql2postgres](https://github.com/maxlapshin/mysql2postgres) 做轉移
  * 因為 db schema 已經透過 Rails 機制完成轉移, 這邊轉移時只需要轉資料部份
