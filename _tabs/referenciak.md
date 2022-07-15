---
title: Referenciák
icon: fas fa-link
image: /assets/img/banner.png
order: 3
show: true
---

## Honlapok

{% assign sites = site.categories['Honlap'] | sort: 'date' | reverse %}
{% include cards.html references = sites %}

## Kiadványok

{% assign publications = site.categories['Kiadvány'] | sort: 'date' | reverse %}
{% include cards.html references = publications %}