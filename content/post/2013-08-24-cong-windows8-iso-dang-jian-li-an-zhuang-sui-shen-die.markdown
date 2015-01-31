---
layout: post
title: "從 windows8 iso 檔建立安裝隨身碟"
date: "2013-08-24T00:13:00+08:00"
comments: true
categories: 
---

步驟大致上是

* 挑隻4G以上的隨身碟
* 把隨身碟格式化成 NTFS 格式
* sudo mount -o loop windows8.iso /mnt/win\_iso
* cp -R /mnt/win\_iso/\* usb\_disk
* sudo sync 
* sudo ms-sys -7 /dev/usb\_disk
