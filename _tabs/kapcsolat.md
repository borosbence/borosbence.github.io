---
title: Kapcsolat
icon: fas fa-envelope
order: 4
show: true
---

Lépjen velem kapcsolatban az alábbi elérhetőségek egyikén:
{% assign email = site.data.contact | find: "type", "email" %}
- <i class="fas {{ email.icon }} fa-fw mr-xl-1"></i> Email: <a href="mailto:{{ site.social.email }}">{{ site.social.email }}</a>
{% assign facebook = site.data.contact | find: "type", "facebook" %}
- <i class="fab {{ facebook.icon }} fa-fw mr-xl-1"></i> Messenger: <a href="{{ facebook.url }}" target="_blank">{{ facebook.url }}</a>