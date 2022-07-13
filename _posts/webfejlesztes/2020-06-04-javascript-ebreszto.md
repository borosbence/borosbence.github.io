---
title: Javascript ébresztő
date: 2020-06-04 18:14:31 +0100
categories: [Webfejlesztés]
tags: [javascript]
img_path: /assets/img/2020/06
image:
  path: alarm.png
  width: 1200
  height: 640
  alt: Javascript ébresztő
---

Egy régebbi [bejegyzésemben]({% post_url webfejlesztes/2018-12-07-ido-megjelenitese-weblapon %}) bemutattam, hogy hogyan kell megjeleníteni a pontos időt Javascript segítségével. Most egy kicsit tovább fejlesztjük ezt a kódsort, hogy egy ébresztőórát állítsunk be webes felületen.

## Ébresztő beállítása

Helyezzünk el két HTML beviteli mezőt az oldalon

```html
<input type="time" id="time" />
<input type="button" onclick="setAlarm()" value="Beállítás">
```

Az első mezőben óra:perc adatot lehet vele megadni, amiben beállítjuk az ébresztő idejét. A második kódsor egy gomb lesz, ami majd a *setAlarm* függvényt fogja elindítani, ezzel beállítva az ébresztőt.

Mielőtt rátérnénk a setAlarm-ra, hozzunk létre egy változót Javascriptben, ami arra szolgál, hogy csak egyszer szóljon az ébresztőóránk, ne végtelenszer értesítsen minket:

```javascript
var notified = false;
```

## Adatok kiolvasása

Majd készítsünk egy *alarm* függvényt (ez még nem a *setAlarm*!), ami lekéri a pontos időt és a beviteli mezőben szereplő értékeket:

```javascript
function alarm() {
  let now = new Date();
  let input = document.getElementById("time").value;
  let alarmHour = parseInt(input.substring(0, 2));
  let alarmMinutes = parseInt(input.substring(3, 5));
```

A sorokban zajló utasítások a következők:

1.  Lekérdezzük az aktuális időt.
2.  Kiolvassuk beviteli mező értékét.
3.  A kiolvasott érték első két karakterét átalakítjuk szám típusúvá, ez lesz az óra.
4.  Ugyanaz mint a 3. lépés csak az utolsó két karakterre, ez lesz a perc.

A kapott értékeket ezután csak össze kell hasonlítani a jelenlegi idővel és küldeni egy értesítést.

```javascript
if (input !== null) {
    if (!notified) {
      if (now.getHours() >= alarmHour &&
        now.getMinutes() >= alarmMinutes) {
        alert('Ébresztő!');
        notified = true;
      }
    }
  }
```

Ahhoz, hogy az ébresztő csak akkor induljon el, amikor már beállítottuk az időt -- tehát ne üres értékkel- létrehozzunk a legfelső *if* feltételt. Itt lesz egy ellenőrzést, hogy az ébresztő csak egyszer szólaljon meg, ne végtelenszer, ezért ellenőrizzük a *notified* változó értékét, amit a program elején **hamisra** állítottunk. Majd össze kell hasonlítani, hogy a jelenlegi óra és perc együtt nagyobb egyenlő-e, mint a megadott idő, ha igen akkor értesít minket kis ablakban a honlap, és utána *notified*-ot **igazra** állítjuk, hogy ne ismételje a még egyszer (végtelenségig).

Emlékszünk még arra, hogy az idő megjelenítésnél mivel időzítettük be a függvényt, hogy másodpercenként lefusson? Itt is ugyanarra a *setInterval*-ralesz szükségünk, hogy mindig az aktuális időt hasonlítsuk össze az ébresztőben megadott értékekkel.

```javascript
function setAlarm() {
  setInterval(alarm, 1000);
}
```

Persze így már nem csak ébresztőt, hanem munkaidő kezdetét, végét be lehet állítani. A továbbfejlesztési lehetőségek tárháza végtelen. 🙂

<iframe src="//jsfiddle.net/bitben/0qpk6c8t/embedded/result,html,js/" width="100%" height="300" frameborder="0" allowfullscreen="allowfullscreen"><span data-mce-type="bookmark" style="display: inline-block; width: 0px; overflow: hidden; line-height: 0;" class="mce_SELRES_start"></span></iframe>