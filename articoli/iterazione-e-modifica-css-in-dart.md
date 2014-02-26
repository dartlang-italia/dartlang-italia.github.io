---
layout: default
title: Iterazione elementi e manipolazione CSS in Dart
autore: Luca Mezzalira
descrizione: In questo tutorial andremo ad esplorare l'iterazione di elementi all'interno di una web application con Dart e come creare alcune animazioni con CSS3
---


In questo tutorial andremo ad esplorare l'iterazione di elementi all'interno di una web application con Dart e come creare alcune animazioni con CSS3.

Il risultato finale su cui ho lavorato è una galleria di immagini con layout responsive:

![Galleria Immagini in Dart 1](http://lucamezzalira.files.wordpress.com/2013/05/wp_20130523_005.jpg)

    

![Galleria Immagini in Dart 2](http://lucamezzalira.files.wordpress.com/2013/05/wp_20130523_008.jpg)

Iterazione
----------

Dart ci da diverse possibilità per iterare elementi all'interno dei nostri progetti:

- tramite un ciclo for in Dart
- con una delle numerose librerie per la gestione di HTML template (tipo underscorejs o mustachejs)
- oppure un MIX tra la parte HTML e la parte scritta in Dart che è quella che prenderemo in esame ora.

Nel mio esempio ho deciso di usare un HTML template, quindi dentro il tag *body* basta inserire un tag *template* che permetterà di iterare tutti i tag che sono al suo interno senza dover scrivere una riga di codice (per chi conosce AngluarJS questa sintassi sarà decisamente familiare)

    <div id="imageCont" class="wrap">
      <template iterate="data in results">
        <div class="image">
          <img id="img_{{data[0]}}" class="faded" src={{data[1]}} width="320" height="240" on-click="onClick($event)" on-mouse-out="onOut($event)" on-mouse-over="onOver($event)" on-load="fadeIn($event)"/>
          <img id="zoom_{{data[0]}}" src="images/zoom.png" class="zoom"/>
          <h2><span>{{data[2]}}</span></h2>
         </div>
      </template>
    </div>
    
Aggiungendo l'attributo *iterate* ad un oggetto vado a recuperare qual è il dataprovider del mio progetto (in questo caso results) e posso andarlo a iterare creando così tanti oggetti quanti sono gli elementi presenti nella mia lista.
Posso inoltre recuperare le proprietà dei miei oggetti presenti nel dataprovider in questa maniera: `{{data[0]}}` oppure `{{data["nomeVariabile"]}}` e inserire questi valori all'interno del mio template.

Un'altra maniera è quella di usare i [Web Components](http://www.dartlang.org/articles/web-ui/#components) come ben descritto in questo articolo presente nella guida in linea di Dart
    
Modificare CSS con Dart
-----------------------

Un altro interessante argomento è come modificare, aggiungere o rimuovere gli stili CSS ad oggetti del DOM tramite Dart.
Ogni elemento del DOM in Dart ha una proprietà chiamata *classes* che permette di aggiungere o eliminare stili CSS in maniera semplice e rapida:

    var myDomObj = query("#idObject");
    myDomObj.classes.remove("class-to-remove");
    myDomObj.classes.add("class-to-add");

Oppure è possibile impostare ogni proprietà di stile direttamente tramite Dart:

    var myDomObj = query("#idObject");
    myDomObj.style
                 ..color = "0xFFF"
                 ..fontSize = "15px";
             
Come potete vedere lavorare con gli stili CSS in Dart è veramente semplice, se volete vedere il progetto integrale potete [scaricarlo da qua](http://www.flairpy.com/dart/imageGallery.zip).



    

