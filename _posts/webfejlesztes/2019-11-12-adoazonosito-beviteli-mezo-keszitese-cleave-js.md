---
title: Adóazonosító beviteli mező készítése - Cleave.js
date: 2019-11-12 16:44:53 +0100
categories: [Webfejlesztés]
tags: [javascript, cleave.js]
image:
  src: /assets/img/posts/cleavejs.png
  width: 1200
  height: 640
  alt: Cleave.js
---

Előfordul, hogy a beviteli mezőkre egészen speciális kéréseket kell alkalmaznunk, hogy mi az amit megengedünk (pl.: számok vagy betűk), és mi az, amiket tiltani szeretnék (pl.: speciális karakterek).

A magyar adószám erre egy jó példa, ahol csak számok szerepelhetnek, de megszabott helyeken van köztük egy elválasztó karakter, hogy átláthatóbb legyen, pl: 12345678-1-12.

Ahhoz, hogy ezt meglehessen valósítani nem elég a szoksásos `<input type="text">`, hiszen ott le kell korlátozni, hogy csak számok lehessenek. Ha csak számokra korlátozzuk le az `<input type="number">` esetében, akkor pedig csak egymás utáni számok lesznek, elválasztás nélkül.

Erre volt segítségem egy JavaScript könyvtár, a [Cleave.js](https://nosir.github.io/cleave.js/), ami a beviteli mezőket korlátozza le a saját feltételeink szerint.

1.  Létre kell hozni egy beviteli mezőt egyedi ID-vel.
2.  Hozzá kell adni a HTML fájl legvégéhez a letöltött cleave.js-t vagy CDN-ről is be lehet linkelni.
3.  Saját feltétel megírása a Cleave.js szabályai szerint.

```html
<input type="text" id="Adoszam" >

<script src="~/Scripts/cleave/cleave.min.js"></script>
<script type="text/javascript">
  var Adoszam = new Cleave('#Adoszam', {
    blocks: [8, 1, 2],
    delimiter: '-',
    numericOnly: true,
  });
</script>
```
Eredmény:

<iframe width="100%" height="300" src="//jsfiddle.net/bitben/v0yb8gp9/embedded/result,html,js/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>