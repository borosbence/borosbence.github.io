---
title: Wordpress multisite network létrehozása
date: 2019-06-16 14:08:53 +0100
categories: [Webfejlesztés]
tags: [wordpress, multisite]
img_path: /assets/img/2019/06/
image:
  path: wp-multisite.png
  alt: Cleave.js
---

Nemrég megörököltem két régi Joomla honlapnak a frissítését. A honlapok már vagy 10 évesek lehettek, így a felújítás helyett két új elkészítését találtam jobb megoldásnak.

Elkezdtem keresni a szolgáltató cPaneljén a Joomla mappákat. Egy tárhely volt igényelve, hozzá pedig két domain cím (**tacedental.hu** és **tacerontgen.hu**). Az egyik Joomla a tárhely *public_html* mappájába lett telepítve, míg a másik az alá tartozó almappába, ami nem mindig szerencsés megoldás, mert így ha nincs lekorlátozva, akkor el tudom érni a tacedental.hu/tacerontgen.hu weboldalt, teljesen kikerülve a tacerontgen.hu címet.

Már majdnem én is elkezdtem volna ugyanígy telepíteni a WordPresseket mikor eszembe jutott, hogy szükségem van nekem kétszer telepíteni ugyanazt? Majdnem ugyanazokat az általam használt plugineket újra beállítani? És persze megjegyezni még egy plusz jelszót? 🙂

![A Joomla főkönyvtárban lévő második Joomla](joomla2in1.jpg)
_A Joomla főkönyvtárban lévő második Joomla_

Egy kis kutakodás után rátaláltam a **[Wordpress Multisite](https://wordpress.org/support/article/create-a-network/)** megoldásra, ami jónak tűnt.

Előnyei:
-   Csak egy WordPress telepítése szükséges.
-   A bővítmények telepítését és az oldal beállításait elég egy helyen kezelni.
-   Elég lesz csak egyszer frissíteni a plugineket és a honlapot.
-   Extra: a regisztrált felhasználók mindkét oldalon betudnak lépni, ugyanazzal a felhasználónév, jelszó párossal.

Ez mind jónak és szépnek tűnik. Ehhez az alábbiakat kellett végrehajtani:

1.  **Mentés**. Mentsük az adatbázist és a honlapot mielőtt belekezdünk a változtatásokba.
2.  A wp.config fájlban keressük ki a következő sort:
```php
/* Ennyi volt, kellemes blogolást! */
```

3.  Ezen sor **felé** másoljuk be ezt a beállítást:
```php
define**(**  'WP_ALLOW_MULTISITE', **true**  **)**;
```

4.  Lépjünk be a WordPressünkbe és az Eszközök / Hálózat telepítésnél végezzük el a szükséges műveleteket az útmutató alapján.
![WordPress oldalak hálózata](network_create.png)
_Ide csak angol nyelvű képet találtam_

5.  Másoljuk be a wp.config-ba és a .htaccess fájlba a kapott kódokat.
![Hálózat telepítés](halozat_telepites.png)
_Beillesztendő kódrészletek_

Ha ezekkel ekészültünk már tudunk új aloldalakat létrehozni a WordPressben, két lehetőséggel:

-   aldomain címmel, pl: *aloldal.fooldal.hu*
-   almappaként, pl: *foldal.hu/aloldal*

Ez már sokaknak nagy segítség, azonban nekem még mindig nem volt megfelelő. A másik domain címet nem írányíthattam át egy almappára vagy aldomain címre, ha minden hivatkozás még a főoldalra fog szólni.

Segítségül hívtam a pluginok istenét és rátáltalám a [**WP Multi Network **](https://hu.wordpress.org/plugins/wp-multi-network/)bővítményre, ami engedélyezi, hogy az oldalainkhoz domain címeket is hozzárendeljünk.

1.  Telepítsük a plugint és aktiváljuk a már létrehozott multisite-on.
2.  Újra nyissuk meg a wp.config fájlt, majd kommenteljük ki a *define DOMAIN_CURRENT_SITE* kezdetű sort.

A teljes config rész így néz ki nálam:
```php
define('WP_ALLOW_MULTISITE', true );
/* WP Multi-Site */
define('MULTISITE', true);
define('SUBDOMAIN_INSTALL', true);
//define('DOMAIN_CURRENT_SITE', 'tacedental.hu');
define('PATH_CURRENT_SITE', '/');

/* Én még ezeket is kikommenteltem: */
//define('SITE_ID_CURRENT_SITE', 1);
//define('BLOG_ID_CURRENT_SITE', 1);
define( 'WP_HOME',    'http://' . $_SERVER['HTTP_HOST'] );
define( 'WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST'] );
```

Az admin felületen a My Networks részen létrehoztam még egy hálózatot, ami a tacerontgen.hu névre hallgat.

![Példa hálózat létrehozására](halozat1.png)
_Példa hálózat létrehozására_

Mindkét domainre létre lett hozva a WordPress honlap, és azon belül is tudok létrehozni majd aloldalakat. Fontos, hogy a honlapokat ne tévesszük össze a hálózatokkal. Minden hálózathoz tartozhat több honlap, egy honlap nem lehet több hálózat része!

Az új lapfüleink, amikkel könyebb lesz a navigáció:


Hálózatok            |  Honlapok
:-------------------------:|:-------------------------:
![Hálózatok](my_networks.jpg)  |  ![Honlapok](honlapjaim.jpg)


Ezek után a cPanelben megadtam, hogy a tacerontgen.hu is a public_html mappára mutasson, így ha mindkét domain is ugyanarra a könyvtárra mutat, akkor is a megfelelő honlapot fogja behozni, a korábbi beállításoknak köszönhetően.

![Domain átirányítása](tacerontgen_cpanel.png)
_Domain átirányítása ugyanarra a wordpress mappára_

Ha pedig az egészet vissza szeretnénk állítani egy oldalra, akkor kövessek [ezt](https://wordx.press/how-to-revert-a-wordpress-multisite-to-a-single-site/) az útmutatót.

Mellesleg ha egy jó fogorvost vagy röntgenest szeretnétek Szegeden, akkor ajánlom nektek a [**TACE Dental**](https://tacedental.hu/)t és a [**TACE Röntgen**](https://tacerontgen.hu/)t! 🙂

Hivatkozások:

-   <https://wordpress.org/support/article/create-a-network/>
-   <https://hu.wordpress.org/plugins/wp-multi-network/>
-   <https://wordx.press/how-to-revert-a-wordpress-multisite-to-a-single-site/>