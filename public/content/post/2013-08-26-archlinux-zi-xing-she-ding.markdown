---
layout: post
title: "Arch Linux 字型設定"
date: "2013-08-26T23:33:00+08:00"
comments: true
categories: desktop linux
---

用了好一陣子的 Arch Linux 搭 KDE, 最近裝新機器時又覺得字型怎麼調都不對, 之前都是採用 [Ubuntu 的字型解決方案](https://wiki.archlinux.org/index.php/Font_Configuration#Ubuntu) 不過這次怎麼調都不順眼, 乾脆改用 [Infinality 的方案](https://wiki.archlinux.org/index.php/Font_Configuration#Infinality), 
直接安裝打包好的整包套件 [infinality-bundle](https://wiki.archlinux.org/index.php/Font_Configuration#Install_from_custom_repository).
然後在 `~/.config/fontconfig/fonts.conf` 裡面加入習慣的字型順序, 像是

```
  <alias binding="strong">
    <family>serif</family>
    <prefer>
      <family>DejaVu Serif</family>
      <family>Droid Sans Fallback</family>
    </prefer>
  </alias>

  <alias binding="strong">
    <family>sans-serif</family>
    <prefer>
      <family>DejaVu Sans</family>
      <family>Droid Sans Fallback</family>
    </prefer>
  </alias>

  <alias binding="strong">
    <family>monospace</family>
    <prefer>
      <family>DejaVu Sans Mono</family>
      <family>Droid Sans Fallback</family>
    </prefer>
  </alias>
```

基本上效果還不錯, 應該會先用一陣子, 哪天不順眼了再調整
