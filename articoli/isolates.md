---
layout: default
title: Isolates&#58; lavorare con il multithreading in Dart
autore: Luca Mezzalira
descrizione: Gli **Isolates** sono la maniera in cui Dart lavora con il multithreading, ma prima di iniziare ad andare più nel dettaglio cerchiamo di capire cos'è e cosa significa lavorare con il multithreading
---

Gli **Isolates** sono la maniera in cui Dart lavora con il multithreading, ma prima di iniziare ad andare più nel dettaglio cerchiamo di capire cos'è e cosa significa lavorare con il multithreading

Multithreading
--------------

Ho trovato questa definizione che reputo decisamente valida: _In informatica il multithreading indica il supporto hardware da parte di un processore di eseguire più thread. Questa tecnica si distingue da quella alla base dei sistemi multiprocessore per il fatto che i singoli thread condividono lo stesso spazio d'indirizzamento, la stessa cache e lo stesso translation lookaside buffer.
Il multithreading migliora le prestazioni dei programmi solamente quando questi sono stati sviluppati suddividendo il carico di lavoro su più thread che possono essere eseguiti in apparenza in parallelo. Mentre i sistemi multiprocessore sono dotati di più unità di calcolo indipendenti per le quali l'esecuzione è effettivamente parallela, un sistema multithread invece è dotato di una singola unità di calcolo che si cerca di utilizzare al meglio eseguendo più thread nella stessa unità di calcolo. Le due tecniche sono complementari: a volte i sistemi multiprocessore implementano anche il multithreading per migliorare le prestazioni complessive del sistema._ ([wikipedia](http://it.wikipedia.org/wiki/Multithreading))

Quando usare i thread?
---------------------

I thread vengono usati ogni qualvolta bisogna fare un'operazione particolarmente intensiva come per esempio aprire il contenuto di un file zip a runtime oppure elaborare grosse mole di dati, insomma in tutte quelle situazione che richiedono calcolo intenso e non vogliamo far frezzare l'interfaccia che sta utilizzando l'utente.

Dart e gli Isolates
-------------------

Prima di iniziare a vedere il codice ho preparato un diagramma che spiega in maniera logica come operano gli Isolate in Dart:

![Isolates in Dart](http://lucamezzalira.files.wordpress.com/2013/06/mockup.png)

Quando si lavora con gli Isolates si hanno sempre dei sender e dei receiver, questo è molto importante da capire perchè questi due "attori" possono operare insieme per creare più thread.
Di default Dart quando lancia un'applicazione viene lanciato il primo Isolate denominato Main Isolate, da questo possiamo lanciare nuovi processi e operazioni intensive direttamente con queste semplici righe di codice:

    port.receive((data, SendPort replyTo){
      replyTo.send("something");
    });

Una delle cose più importanti da ricordarsi quando si lavora con gli Isolate è che se stiamo attendendo un dato da un altro Isolate bisogna mettere in ascolto il Main Isolate istanziando il receiver presente in ogni Dart application, in questo modo il programma attenderà che gli altri Isolate facciano il lavoro "sporco" per poi passare i risultati al Main Isolate, altrimenti il programma terminerebbe senza aspettare alcun dato e quindi l'utilizzo degli altri Isolate sarebbe vano.

Ho creato un piccolo esempio che potete vedere direttamente su [youtube](http://youtu.be/2ljB4069ais)

L'esempio è basato sugli Isolate e sostanzialmente ho creato 2 link che lanciano la stessa routine ma in maniera diversa ovvero il link con scritto _MONO_ lancerà un paio di cicli for con molti elementi da iterare e li lancerà uno di seguito all'altro bloccando di conseguenza la riproduzione dell'animazione CSS creata ad hoc per far vedere il freeze dell'interfaccia, mentre se clicchiamo su _ISOLATE_ lanceremo 2 Isolate che eseguiranno i 2 cicli for in parallelo con conseguente recupero di tempo e soprattutto senza bloccare l'esecuzione del Main Isolate.

Per lanciare un Isolate in Dart basterà scrivere:

    var isolate = spawnFunction(bigForCycle);
    isolate.send("data", port.toSendPort());


Dopodiche nella funzione bigForCycle possiamo elaborare quello che vogliamo avendo la certezza di lavorare all'interno di un nuovo Isolate:

    void bigForCycle(){
      port.receive((data, SendPort replyTo){
        var count;
        for(var i = 0; i < FINAL_AMOUNT; i++){
          count = i;
        }
        replyTo.send(count);
      });
    }

Infine ricordiamoci sempre di chiudere gli Isolate creati una volta che ci han risposto in modo da ottimizzare le performance del nostro progetto:


    port.receive((data, SendPort replyTo){
      counterIsolate++;
      end = new DateTime.now();
      if(counterIsolate == 2){
        var finalTime = end.difference(start).toString();
        myH1.appendHtml("isolate total time: <b>$finalTime</b><br/>");
        // if you want to close isolates you have to use the line below
        port.close();
      }
    });
 
Ci sono altri modi di lavorare con gli Isolate, nel mio caso ho lavorato con Isolate interni alla Main Class ma possiamo anche lavorare con Isolate in file differenti come spiegato nel [post di Seth Ladd](http://blog.sethladd.com/2013/04/dynamically-load-code-with-dart.html).

Potete trovare i sorgenti dell'esempio direttamente a questo [link](https://github.com/lucamezzalira/Dart-Samples/).