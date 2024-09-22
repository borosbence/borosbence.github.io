---
title: Css fájl minification átalakítás
date: 2019-02-18 21:59:09 +0100
categories: [Programozás, Webfejlesztés]
tags: [css]
media_subpath: /assets/img/2019/02/
image:
  path: css-minify.png
  alt: Css minification
---

Prestashop csinosításánál futottam bele abba, hogy az eredeti (Classic 1.7.5) téma minification-ön, vagyis miniatűrizáción esett át. Viszont valahogyan csak bele kellett nyúlnom a kódba. Hogy ez mit is takar? Ezt:

![11232 soros fájl átalakítva 1 sorrá](minification.jpg)
_11232 soros fájl átalakítva 1 sorrá_

A minificationt- az internet hőskorában találták ki, hogy a lassú sávszélességen könnyebb legyen letölteni a stílus és script fájlokat a weblapról. Kiszedi a felesleges szóközöket, sortöréseket, kommenteket, csökkentve a fájl méretét. Manapság mikor már mindenki mobilon szedi le a gigabájtokat nem sok értelme van így a kilobájtokkal így spórolni, plusz még magunkkal kicseszni az átláthatlan kód miatt. Üzleti környezetben, ahol elkülönül a fejlesztői-teszt-éles rendszer még van értelme. Szerencsére találtam egy jó online konvertáló honlapot, ahol  könnyen lehet szépíteni és kicsinyíteni tudjuk a CSS kódunkat: <https://codebeautify.org/css-beautify-minify> A Beutify gombbal szépíthetünk a Minify-al pedig tömöríthetjük a kódunkat.
