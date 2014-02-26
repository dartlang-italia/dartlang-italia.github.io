---
layout: default
title: Archivio News
---

### Archivio News

{% for post in site.posts %}
###[{{post.title}}]({{post.url}})  
_Pubblicato in data {{ post.date | date: "%d/%m/%Y" }}_
{% endfor %}