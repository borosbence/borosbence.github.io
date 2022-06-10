---
title: Referenciák
icon: fas fa-link
order: 2
show: true
---

## Honlapok

{% assign sites = site.categories['Honlap'] | sort: 'date' | reverse %}
{% include cards.html references = sites %}

## Kiadványok

{% assign publications = site.categories['Kiadvány'] | sort: 'date' | reverse %}
{% include cards.html references = publications %}