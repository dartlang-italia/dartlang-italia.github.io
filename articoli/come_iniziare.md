---
layout: default
title: Come iniziare a programmare in Dart
autore: Claudio d'Angelis
descrizione: 
autoreoriginale: 
titolooriginale: 
urloriginale: 
---

In questo articolo vedremo come iniziare a programmare in Dart
utilizzando lo strumento di sviluppo principale, ovvero **Dart Editor**.

**Dart Editor**, un _fork_ di [Eclipse](http://eclipse.org), è un tool molto potente dotato di caratteristiche comuni
a molti altri ad altri _IDE_ come ad esempio il completamento automatico (basato sulla documentazione dell'API),
l'abilità di suggerire dei _quick fix_ nel caso in cui il codice contenga errori,
e l'integrazione con gli altri tool del _Software Development Kit_ di Dart.

Innanzitutto scarichiamo l'editor dal sito ufficiale all'indirizzo
[http://www.dartlang.org/tools/editor/](http://www.dartlang.org/tools/editor/); il pacchetto include:

- **DartEditor**
- **dart**, la macchina virtuale
- **Dartium**, un _fork_ di Chromium che incorpora la macchina virtuale
- **dart2js**, il compilatore Javascript
- **pub**, il gestore di pacchetti
- **dartanalyzer**, tool per l'analisi della correttezza del codice
- **dartdoc**, tool per l'autogenerazione della documentazione di un progetto

Avviamo l'editor e scegliamo di creare una nuova applicazione, **File -> New Application**:

![Screenshot](/img/posts/darteditor_nuovaapplicazione.png)

Come vedete nello screenshot, l'editor offre la possibilità di generare
del **codice di esempio** per 4 tipi di applicazione:

- **Chrome packaged application**, una web app che è eseguita all'interno del browser Google Chrome e che può utilizzare [le API chrome.*](https://developer.chrome.com/extensions/api_index.html).
- **Command-line application**, applicazione da riga di comando (e.g. un web server, uno script, etc)
- **Web application**, una classica web application
- **Web application (using the web_ui library)**, una web application che utilizza la libreria **web_ui** che definisce funzioni per la gestione, fra le altre, di **Web Components** e **Model-driven Views**

Scegliamo di generare il codice di esempio per **Web Application**:

![Screenshot](/img/posts/darteditor_codicediesempio.png)

L'editor ha generato una semplice web app di esempio che mostra una casella di testo che all'evento _onClick_
inverte i caratteri che compongono la stringa di testo contenuta.

Per avviare l'applicazione utilizziamo il comando **Run -> Run**, o in alternativa diamo **CTRL** (o CMD) + **R**.

![Screenshot](/img/posts/darteditor_webappesempio.png)

L'applicazione è stata eseguita in **Dartium** e utilizza il codice Dart nativo. Per provare questa applicazione in un qualsiasi
browser non dobbiamo fare altro che **compilare** l'applicazione in Javascript; per fare questo andiamo, dall'editor, su
**Tools -> Generate Javascript**.

Come avete visto, muovere i primi passi in Dart è semplicissimo e ci vogliono solo pochi minuti per essere operativi.  
Consultate la [Panoramica del linguaggio](/doc.html) per avere una panoramica
sui tipi di dato, sulla struttura di un'applicazione e sulle altre caratteristiche di Dart.