---
layout: post
title: "elasticsearch入門筆記(1)"
date: "2012-02-25T16:08:00+08:00"
comments: true
categories: elasticsearch
---

## 角色關係對照

elasticsearch 跟 MySQL 中定義資料格式的角色關係對照表如下

<table class="midden center">
  <thead><tr><th>MySQL</th><th>elasticsearch</th></tr></thead>
  <tbody>
    <tr><td>database</td>     <td>index</td></tr>
    <tr><td>table</td>        <td>type</td></tr>
    <tr><td>table schema</td> <td>mapping</td></tr>
    <tr><td>row</td>          <td>document</td></tr>
    <tr><td>field</td>        <td>field</td></tr>
  </tbody>
</table>

<!-- more -->

## 設定檔與目錄配置

參考: 

* http://www.elasticsearch.org/guide/reference/setup/dir-layout.html
* http://www.elasticsearch.org/guide/reference/setup/configuration.html

## 建立 index

`curl -XPUT 'http://localhost:9200/my_index_name/'` 這邊的 `my_index_name` 就是要被建立的 index 的名字

## 設定 index 

```
curl -XPUT 'localhost:9200/my_index_name/_settings' -d '
{
    "index" : {
        "number_of_replicas" : 4
    }
}
'
```  
可以設定的參數在 [這頁](http://www.elasticsearch.org/guide/reference/api/admin-indices-update-settings.html)

## 建立 type 與 mapping

在預設的情況下 elasticsearch 會自動建立 type 跟 mapping, 但是我會建議自行定義 mapping 的內容, 因為這樣可以做出比較符合需求的設定,例如不同的欄位要使用不同的分詞方式

下面的範例中 `my_index_name`, `my_type_name`, `my_field_name` 都是自己命名的 
```
curl -XPUT 'http://localhost:9200/my_index_name/my_type_name/_mapping' -d '
{
    "my_type_name" : {
        "properties" : {
            "my_field_name" : {"type" : "string", "store" : "yes"},
            "my_field_name2" : {"type" : "string", "store" : "yes"},
            "my_field_name3" : {"type" : "date", "store" : "yes"}
        }
    }
}
'
```
每個欄位的定義, 會像是 `"my_field_name" : {"type" : "string", "store" : "yes"}` 這樣子,
常用的欄位格式定義可以參考[這頁](http://www.elasticsearch.org/guide/reference/mapping/core-types.html)
 
## 建立搜尋資料

```
curl -XPOST 'localhost:9200/my_index_name/my_type_name/1' -d ' 
{
    "my_field_name" : "test",
    "my_field_name2": "test2",
    "my_fidle_name3": "2012-02-1"
}
'
``` 

`my_index_name/my_type_name/1` 中的`1`代表的是這比資料的`id`, 建議寫入資料庫裏面的 primaty key, 如果沒有設定的話, 會自動產生像是`EYQqF6eyTJGJ-6aZiIYZsg`的內容

## 搜尋資料

```
curl -XPOST 'localhost:9200/my_index_name/my_type_name/_search' -d '
{ 
    "query":{ 
        "text":{ "_all":"test"}
    }
}
'
```

詳細搜尋的語法需參考[這頁](http://www.elasticsearch.org/guide/reference/query-dsl/)
