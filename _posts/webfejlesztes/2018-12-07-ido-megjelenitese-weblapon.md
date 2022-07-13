---
title: Idő megjelenítése weblapon
date: 2018-12-07 22:10:24 +0100
categories: [Webfejlesztés]
tags: [javascript]
img_path: /assets/img/2018/12
image:
  path: js-ora.jpg
  width: 1200
  height: 640
  alt: Idő megjelenítése weblapon
---

Javascripttel a weblapunkon könnyedén megjeleníthetünk egy dinamikusan frissülő órát, dátummal. A következő pár sorban bemutatom, hogy hogyan kell.

Először helyezzünk el a HTML oldalunkon egy elemet, ahol az időt akarjuk megjeleníteni.

```html
<div id="time"></div>
```

Kell egy segítő függvény, amivel a 10 alatti számokhoz hozzábiggyeszt egy 0-át. pl: e helyett: 2019.1.1. / 1:1:1: helyett 2019.01.01. / 01.01:01 lesz.

```javascript
function addZero(i) {
    if (i < 10) {
        i = "0" + i;
    }
    return i;
}
```

Létrehozunk egy függvényt, ami majd lekérdezi a jelenlegi dátumot.

A jelenlegi dátumot eltároljuk a **now** változóban.

```javascript
function updateClock() {
    var now = new Date();
```

Egy új változóban a **now** időegységeit lekérdezzük, majd kettősponttal elválasztva eltároljuk.

```javascript
var time = addZero(now.getHours()) + ':' + addZero(now.getMinutes()) + ":" + addZero(now.getSeconds());
```

Ugyanígy a dátummal. A hónapnál egyet hozzáadunk, mert 0-val kezdődik a számolás.

```javascript
var date = addZero(now.getUTCFullYear()) + '.' + (addZero(now.getUTCMonth() + 1)) + "." + addZero(now.getUTCDate());
```

Megjelenítjük a HTML elemben, és összefűzzük a dátumot és az időt.

```javascript
document.getElementById('time').innerHTML = [date, time].join(' / ');}
```

Ismételjük meg az elkészült függvényt 1 másodpercenként (1000 miliszekundum).

```javascript
setInterval(updateClock, 1000);
```

Később a HTML-ben vagy a Javascript fájlban meghívható az **updateClock()** függvény.

A teljes kódsor az eredménnyel:

<iframe src="//jsfiddle.net/bitben/015woLv2/embedded/result,html,js/" width="100%" height="300" frameborder="0" allowfullscreen="allowfullscreen"><span data-mce-type="bookmark" style="display: inline-block; width: 0px; overflow: hidden; line-height: 0;" class="mce_SELRES_start"></span></iframe>