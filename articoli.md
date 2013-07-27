---
layout: default
title: Articoli e tutorial
---

##{{ page.title }}


{% for articolo in site.pages %}

{% if articolo.url contains '/articoli/' %}

- #### [{{articolo.title}}]({{articolo.url}})  
    {% if articolo.titolooriginale == nil %}
    _di [{{articolo.autore}}]({{site.staff[articolo.autore].url}})_  
    {{articolo.descrizione}}  
    {% else %}
    _articolo originale: [{{articolo.titolooriginale}}]({{articolo.urloriginale}}) di {{articolo.autoreoriginale}}_  
    _traduzione di [{{articolo.autore}}]({{site.staff[articolo.autore].url}})_

    {{articolo.descrizione}}  
    {% endif %}
{% endif %}

{% endfor %}