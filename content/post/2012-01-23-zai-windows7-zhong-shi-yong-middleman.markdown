---
layout: post
title: "在 windows7 中使用 middleman"
date: "2012-01-23T03:58:00+08:00"
comments: true
categories: 
---

步驟
---

1. 下載 RubyInstaller 與 Devkit
    * 到 <http://rubyinstaller.org/downloads/>
    * 下載 Ruby 1.9.3-p0 <http://rubyforge.org/frs/download.php/75465/rubyinstaller-1.9.3-p0.exe>
    * 下載 Devkit <https://github.com/downloads/oneclick/rubyinstaller/DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe>
1. 安裝 RubyInstaller
    * 安裝過程勾選 
        1. Add Ruby executables to your Path
        1. Associate .rb and .rbw files with thus ruby installation
1. 解開 Devkit
    * 點選下載回來的 DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe 便可解開到特定目錄中
1. 安裝 middleman 
    * 在剛剛解開的 Devkit 目錄中點選 msys 批次檔案
    * 在彈出的視窗中輸入 `gem install middleman`
1. 啟動 PowerShell 即可使用 middleman, 指令請參考 <http://middlemanapp.com>
    
    

      
