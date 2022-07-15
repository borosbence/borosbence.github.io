---
title: Wordpress gyerek téma létrehozása
date: 2018-12-17 10:29:23 +0100
categories: [Webfejlesztés]
tags: [wordpress, child-theme]
# img_path: /assets/img/2018/12
image:
  path: /assets/img/2018/12/wordpress-child-themes.jpg
  width: 1200
  height: 640
  alt: Wordpress szülő és gyermek
---

A neten sok jó WordPress sablon téma van, de sosincs olyan, amilyennek elképzeltük a saját weblapunkat. A téma testreszabása fülön, általában csak pár beállítás van, nem mindig az, amit szeretnénk. Ha jobban szeretnénk testre szeretnénk szabni, akkor kezdhetünk el a kódsorok közt kutatni...

Első lépésként vértezzük fel magunkat kellő programozói tudással és hozzunk létre egy gyerek témát (child theme) a kiválasztott sablonból,.
A neten sok jó WordPress sablon téma van, de sosincs olyan, amilyennek elképzeltük a saját weblapunkat. A téma testreszabása fülön, általában csak pár beállítás van, nem mindig az, amit szeretnénk. Ha jobban szeretnénk testre szeretnénk szabni, akkor kezdhetünk el a kódsorok közt kutatni...

Első lépésként vértezzük fel magunkat kellő programozói tudással és hozzunk létre egy gyerek témát (**child theme**) a kiválasztott sablonból,.

# Mire jó a wordpress gyerek téma?

*(A sima gyermek már egy másik ***téma***🙂)

Tetszik pl: a *Twenty Seventeen* sablon, de kicsit testre szeretném szabni. Azonban ha jön hozzá egy frissítés, akkor telepítésnél sajnos az összes eddigi kódsor módosításunk elveszthet. A frissítés elhalasztása pedig biztonsági szempontból problémás...\
Erre a megoldásra jó a gyerek-téma, ami kiegészíti a főtéma hiányosságait, a mi igényeinknek megfelelően. Használatához szükséges a szülő téma megléte a webtárhelyen.

## Hogy hozzunk létre gyerek témát?

Készítsünk egy új mappát a wp-content/themes könyvtárban pl: twentyseventeen-child néven, ha a WordPress (későbbiekben WP) twentyseventeen témájából indulunk ki.

Innentől két lehetőségünk van:

1.  Átmásoljuk az eredeti téma style.css-ét.
2.  Létrehozunk egy saját style.css fájlt.

# Saját stíluslapunk

A styles.css első soraiba másoljuk be az alábbi kódsor részletet vagy írjuk át a meglévőt.

```php
/*
Theme Name: Twenty Seventeen Child
Theme URI: https://wordpress.org/themes/twentyseventeen/
Author: Boros Bence
Author URI: http://borosbence.webtelek.hu
Description: Egyedi kéréseket ebbe a sablonba gyűjtöttem össze. 
    A Catch Box téma telepíse is szükséges a használatához, de bekapcsolva ennek kell lennie.
Version: 1.0
Template: twentyseventeen
Text Domain: twentyseventeen-child
Domain Path: /languages/
Tags: linlib, custom-design
*/
```

Egészítsük ki saját adatainkkal, ha csak nem valaki más hírnevét szeretnénk tovább növelni 🙂 .\
Fontos, hogy a **Template:** résznél a szülő téma mappa nevét írjuk be, így fogja tudni a WP, hogy a kis témánknak nevelőre van szüksége.

A kikommentelt rész fog megjelenni később a sablonunk részleteinél.

Ha az üreset töltjük fel a weblapra és aktiváljuk a témát, akkor egy kellemetlen meglepetésben lehet részünk *(nem talál a WP egyetlen szabályt sem, amit alkalmazna)*. Ehhez még létre kell hozni a *functions.php*-t.

# A php függvényeink

Beállíthatjuk, hogy a szülő téma stíluslapja után a mi fájlunk töltődjön be, így felülírva az eredeti CSS szabályokat.\
Ezzel a megoldással nem készül felesleges másolat és átláthatóbb lesznek a saját módosításaink a styles.css-ben.

Hozzuk létre a functions.php-t, ugyanabban a könyvtárban, mint a styles.css-t. Illesszük be az alábbi kódrészletet:

```php
<?php
add_action( 'wp_enqueue_scripts', 'my_theme_enqueue_styles' );
function my_theme_enqueue_styles() {
    $parent_style = 'twentyseventeen';
    wp_enqueue_style( $parent_style, get_template_directory_uri() . '/style.css' );
    wp_enqueue_style( 'twentyseventeen-child',
        get_stylesheet_directory_uri() . '/style.css',
        array( $parent_style ),
        wp_get_theme()->get('Version')
    );
}
?>
```

Persze itt is írjuk át a saját témánk neveit. Én szülőnek a twentyseventeen-t használom, gyerekként pedig a twentyseventeen-child-ot a *wp_enqueue_style* függvénynél.

A szülő téma screenshot.png képét akár ki is cserélhetjük és bemásolhatjuk a témánk könyvtárába ugyanezzel a névvel.\
Töltsük fel a mappánkat a webszerver megfelelő mappájába.\
Alkotásunkat bekapcsolhatjuk Megjelenítés / Sablonok fülön.

Végeredmény:

![Catch Box Child témám, zöld kerettel jelölve, pirossal az eredeti](/assets/img/2018/12/child-theme.png)
_Catch Box Child témám, zöld kerettel jelölve, pirossal az eredeti_