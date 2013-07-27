---
layout: default
title: pub, il gestore di pacchetti
autore: Fabio Benedetti
descrizione: Introduzione a pub, il package manager di Dart
autoreoriginale: Dart Team
titolooriginale: Getting Started
urloriginale: http://pub.dartlang.org/doc/
---

_Pub_ è il gestore dei pacchetti (_packages_) in Dart, ci aiuta a riutilizare il codice Dart  esistente e impacchetta le applicazioni con le relative librerie così da renderle riutilizzabili e condivisibili con altre persone. Pub si occupa della gestione delle dipendenze e delle versioni di un'applicazione in modo da poter essere certi che essa possa essere eseguita su altre macchine.

Per **individuare** un package presente su [pub.dartlang.org](http://pub.dartlang.org), si utilizza l'apposito Search Box in alto a
destra.

Per **utilizzare** un package che è presente su pub.dartlang.org:

1. Si crea il file `pubspec.yaml` (se non esiste già) e si inserisce il package come dipendenza. Per esempio, per utilizzare il package [web_ui](http://pub.dartlang.org/packages/web_ui) in un applicazione, `pubspec.yaml` conterrà queste istruzioni:  

        name: my_app
        dependencies:
            web_ui: any

2. Si esegue `pub install` tramite linea di comando oppure attraverso il menu del  Dart Editor: Tools > Pub Install.  
3. Si Importa una o più librerie dal package:

        import 'package:web_ui/web_ui.dart';  


## Installazione e configurazione di pub

Pub fa parte di [Dart SDK](http://www.dartlang.org/docs/sdk/) (Software Development Kit), e può essere ottenuto singolarmente oppure come
parte del [Dart Editor](http://dartlang.org/editor). Pub può essere usato da riga di comando, `pub`, oppure può essere lanciato dall'interno
del Dart Editor.

Per utilizzare `pub` e altri strumenti da linea di comando, è necessario aggiungere la cartella `bin` contenuta nel SDK ai percorsi di sistema. Per esempio, su Mac e Linux:

    export PATH=$PATH:<percorso/di/dart>/bin

Per `<percorso/di/dart>/` si intende il percorso assoluto della cartella principale in cui risiedono le SDK. Per esempio, se installi Dart Editor in `/home/me/dart`, aggiungi questo percorso a PATH:  

    /home/me/dart/dart-sdk/bin

Su Windows, puoi settare la variabile d'ambiente PATH attraverso il Pannello di Controllo. Una veloce [ricerca](https://www.google.com/search?q=windows+set+environment+variable&qscrl=1) può essere utile ad individuare le istruzioni specifiche per una specifica versione di Windows.

## Creare un package

Per pub un package è una directory che contiene il codice Dart e il materiale necessario alla sua esecuzione, come risorse, test e documentazione. Frameworks e librerie riutilizzabili sono ovviamente packages, ma anche le applicazioni lo sono. Per far sì che un'applicazione utilizzi un package,
deve essere anch'essa definita come package.

Mentre tutto è un package in pub, ci sono due tipologie di packages che in pratica sono utilizzati in maniera leggermente diversa. I [library packages](http://pub.dartlang.org/doc/glossary.html#library-package) sono dei packages scritti allo scopo di essere utilizzati da altri packages. Solitamente sono composti da codice importato da altri packages, e sono ospitati in repository ai quali altri utenti possono accedere. Un [application package](http://pub.dartlang.org/doc/glossary.html#application-package) _utilizza_ packages, ma di per se non è riutilizzato. In altre parole, i library packages sono usati come dipendenze, ma gli application packages no.

Nella maggior parte dei casi non ci sono differenze tra le due tipologie di package e li chiameremo semplicemente "packages". Soltanto nei casi in cui è necessario fare distinzione, sarà specificato "application package" o "library package".

Per trasformare la tua applicazione in un application package, in modo che possa utilizzare altri packages, è necessaria la presenza di un file __pubspec__. Questo file è scritto usando [YAML language](http://yaml.org/ "YAML language") ed è chiamato `pubspec.yaml`. Il più semplice pubspec.yaml possibile contiene soltanto il nome del package. Questo file deve essere salvato come `pubspec.yaml` nella directory root della tua applicazione.

Ecco la più semplice tipologia di file `pubspec.yaml`:  

    name: my_app 

Ora `my_app` è un package.

##Aggiungere una dipendenza

Uno dei principali compiti di pub è gestire le __dipendenze__. Una dipendenza non è altro che un package che viene utilizzato da un'applicazione.
Se una applicazione sta utilizzando alcune librerie di trasformazione chiamate "transmogrify", questo application package dipenderà dal package `transmogrify`.

Le dipendenze di un application package vanno specificate immediatamente dopo il nome del package, per esempio:

    name: my_app
    dependencies:
        transmogrify

Attraverso questo codice, viene dichiarata la dipendenza dal pacchetto (fittizio) `trasmogrify`.

## Installare le dipendenze

Una volta dichiarata una dipendenza è necessario che pub la installi. Se si sta utilizzando Dart Editor, va selezionato "Pub install" dal menu "Tools". Se si preferisce invocare pub da linea di comando, va eseguito:

    $ cd percorsp/a/my_app  
    $ pub install

Al momento questo comando deve essere eseguito all'interno della directory contenente il file pubspec.yaml, in futuro potrà essere eseguito in qualsiasi sottodirectory del package.

Quando viene eseguito questo comando pub crea una directory `packages` nella cartella dove è presente il file `pubspec.yaml`. Qui saranno scaricati ed installati tutti i packages dai quali l'application package dipende (queste sono chiamate __dipendenze immediate__). Tutti questi packages saranno ispezionati e sarà a sua volta installato tutto ciò da cui _essi_ dipendono, ricorsivamente (queste sono chiamate __dipendenze transitive__).

Quando ciò è stato fatto si avrà una directory `packages` contenente tutti i singoli package necessari all'esecuzione del programma.



##Importare codice dalle dipendenze

Ora che le dipendenze sono correttamente installate, dobbiamo importarle nel nostro progetto. Per accedere ad una libreria da un altro package si usa l'istruzione `import` con lo schema `package:libreria/file_della_libreria.dart`:

    import 'package:transmogrify/transmogrify.dart';

Questo comando ispeziona il package `transmogrify` cercando, nella radice della directory, un file chiamato `transmogrify.dart`. La maggior parte dei package definisce un singolo entrypoint il cui nome è lo stesso del package. Si controlli la documentazione del package per verificare se nello specifico esistano differenze riguardo l'importazione del codice.

Conviene sempre controllare la directory `packages` (generata da pub). Se contiene errori, la directory packages potrebbe non essere aggiornata. Per risolvere questo problema bisogna eseguire `pub install` tutte le volte che si modifica il pubspec.

È inoltre possibile utilizzare questo stile per importare le librerie dall'interno di un pacchetto. Per esempio, se questa è la struttura del pacchetto

    transmogrify/
        lib/
            transmogrify.dart
            parser.dart
        test/
            parser/
                parser_test.dart

Il file `parser_test.dart` _potrebbe_ importare `parser.dart` in questo modo:

    import '../../lib/parser.dart';

Questo schema, che utilizza percorsi relativi, è difficile da leggere; inoltre se `parser_test.dart` venisse spostato in un altra directory, questo percorso non sarebne più valido e 
si dovrebbe modificare manualmente il percorso. Utilizzando lo schema `package:libreria/file.dart`:

    import 'package:transmogrify/parser.dart';

In questo modo, il comando `import` potrà sempre reperire il file `parser.dart` indipendentemente da dove essp si trovi.

##Aggiornare una dipendenza

Quando si installa per la prima volta una nuova dipendenza, `pub` scarica automaticamente l'ultima versione compatibile con gli altri packages presenti nell'applicazione. In seguito viene bloccato il package, in modo tale che venga utilizzata _sempre_ quella versione, attraverso la creazione di un __lockfile__. Questo file è chiamato `pubspec.lock` ed è creato da `pub`. In questo file vengono esplicitate tutte le versioni di ogni dipendenza (immediata o transitiva) utilizzata dal package.

Se il package è di tipo application, si potrà controllare questo file direttamente all'interno del codice. In questo modo, ci si assicura per chiunque stia lavorando sull'applicazione utilizzi la stessa versione di tutti i packages. Ciò fa anche si che vemga utilizzata la stessa versione delle dipendenze quando si effettua il deploy dell'applicazione in produzione.

Per **aggiornare** le dipendenze di un'applicazione si utilizza il comando `update`:

    $ pub update

Questo comando fa in modo che pub rigeneri il lockfile utilizzando le ultime versione dei pacchetti da cui dipende l'applicazione.
Se si intende aggiornare soltanto una specifica dipendenza, la si può specificare in questo modo:

    $ pub update transmogrify

Questo comando aggiorna `transmogrify` all'ultima versione, ma lascia gli altri packages invariati.

##Pubblicare un pacchetto

Pub non serve soltanto per importare i packages creati da altre persone. Permette anche di condividere i propri packages con il mondo. Una volta sviluppato del codice utile, se si vuole che chiunque altro possa utilizzarlo, basta eseguire questo comando:

    $ pub publish

Pub controlla che il nostro package sia conforme al [formato pubspec](http://pub.dartlang.org/doc/pubspec.html) ed alle [convenzioni di layout per i packages](http://pub.dartlang.org/doc/package-layout.html "convenzioni di layout per il package"), e in seguito effettua l'upload verso [pub.dartlang.org](http://pub.dartlang.org/ "pub.dartlang.org"). A questo punto ogni utente di Pub avrà la possibilità di scaricare o di utilizzare il package come dipendenza attraverso il proprio proprio pubspec. Per esempio, se abbiamo pubblicato la versione 1.0.0 di un package chiamato `transmogrify`, gli utenti potranno utilizzare questa dipendenza:

    dependencies:
        transmogrify: ">= 1.0.0 < 2.0.0"

Si tenga presente che i package rimangono pubblicati per sempre. Non appena si pubblica un package molto utile, gli utenti potranno utilizzarlo nelle loro dipendenze. Una volta iniziato ciò, rimuovere un package vole dire rendere inutilizzabile le loro applicazioni. Perchè non accada, pub sconsiglia fortemente la cancellazione dei packages. Si potrà sempre effettuare l'upload di una nuova versione dei packages, ma le precedenti versioni continueranno ad essere disponibili per gli utenti che non sono pronti per effettuare l'aggiornamento. 