---
layout: default
title: Usare API basate su Future
autore: Claudio d'Angelis
descrizione: 
autoreoriginale: Shailen Tuli
titolooriginale: Using Future Based APIs
urloriginale: http://www.dartlang.org/articles/using-future-based-apis/
---

_Articolo originale: [Using Future Based APIs](http://www.dartlang.org/articles/using-future-based-apis/)_


## Introduzione

Dart è un linguaggio _single-threaded_. Se una determinata funzione blocca il _thread_ dell'esecuzione, il programma si ferma. Diamo uno sguardo ad un esempio concreto:

	import 'dart:io';

	void stampaLeNotizieDelGiorno() {
	  File file = new File("notizieDelGiorno.txt");
	  print(file.readAsStringSync());
	}

	void main() {
	  stampaLeNotizieDelGiorno();
	  stampaNumeriDellaLotteria();
	  stampaPrevisioniDelTempo();
	  stampaRisultatiBaseball();
	}

Il nostro programma legge le notizie del giorno da un file, `notizieDelGiorno.txt`, le stampa, e successivamente stampa una serie di altri elementi di interesse per l'utente:

	<Contenuto di notizieDelGiorno.txt>
	Numeri vincenti del lotto: [23, 63, 87, 26, 2]
	Previsioni per domani: 21C, soleggiato.
	Risultati del Baseball: Red Sox 10, Yankees 0


Il nostro codice è problematico: siccome `readAsStringSync()` blocca, il codice restante viene eseguito solo quando readAsStringSync()` restituisce i contenuti del file, _however long that takes_. Quindi se la lettura del file impiega molto tempo, l'utente aspetta passivamente, chiedendosi se abbia vinto la lotteria, o come sarà il tempo per domani o chi abbia vinto la partita. E non va bene.

Per garantire che l'applicazione sia _reattiva_, gli autori di Dart utilizzano un modello asincrono per definire funzioni possono impiegare molto tempo. Tali funzioni restituiscono il proprio valore usando un oggetto **Future**.

## Cos'è un oggetto _Future_?

Un Future rappresenta un'indicazione per ottenere un valore che sarà disponibile in futuro. Quando una funzione che restituisce un Future viene invocata, accadono due cose:

1. La funzione accoda il lavoro da fare e restituisce immediatamente un oggetto Future _incompleto_.
2. Successivamente, quando il valore diventa disponibile, il Future _si completa_ con quel valore (oppure con un errore, come discuteremo successivamente).

Per ottenere il valore che un Future rappresenta, si usa il metodo `then()` per registrare una funzione di _callback_. Questa callback si attiva quando il Future è completo.

## Usare un Future

Riscriviamo `stampaLeNotizieDelGiorno()` per ottenere il contenuto in modo asincrono:

	import 'dart:io';
	import 'dart:async';

	void stampaLeNotizieDelGiorno() {
	  File file = new File("notizieDelGiorno.txt");
	  Future future = file.readAsString();
	  future.then((contenuto) {
	    print(contenuto);
	  });
	}

La funzione `stampaLeNotizieDelGiorno()` ora usa `readAsString()`, che non è _bloccante_. La chiamata di `readAsString()` accoda il lavoro da fare ma non interrompe l'esecuzione del codice restante. Il programma stampa i numeri della lotteria, il meteo e il punteggio del baseball; quando `readAsString()` completa la lettura del file con le notizie, il programma ne stampa il contenuto. Se `readAsString()` impiega un po' di tempo per completare il lavoro, non succede niente di grave: l'utente nel frattempo legge le altre informazioni.

	Numeri vincenti del lotto: [23, 63, 87, 26, 2]
	Previsioni per domani: 21C, soleggiato.
	Risultati del Baseball: Red Sox 10, Yankees 0
	<Contenuto di notizieDelGiorno.txt>

## Sequenza di eventi durante l'esecuzione del codice

Il codice precedente viene eseguito in tre fasi:

1. Il programma comincia con `main()`, che chiama `stampaLeNotizieDelGiorno()`, che accoda l'attività di lettura del file.
	Dopo aver chiamato le funzioni rimanenti, `main()` termina, ma il programma resta in esecuzione.

2. Il lavoro programmato da `readAsString()` viene eseguito, e i contenuti del file vengono letti in memoria. Quando la lettura del file è completa, il Future si completa con i contenuti del file.

3. La funzione di callback registrata nel metodo `then()` si attiva e stampa i contenuti del file con le notizie del giorno.

Chiamando `then()` si ottiene un nuovo Future, che si completa con il valore restituito dalla callback di `then()`. Questo significa che le chiamate a `then()` possono essere concatenate, come vedremo successivamente.

## Gestire gli errori dei Futures

Se una funzione che restituisce un Future si completa con un errore, il Future restituito da `then()` si completa, a sua volta, con un errore. Possiamo catturare quell'errore usando `catchError()`:

	void stampaLeNotizieDelGiorno() {
	  File file = new File("notizieDelGiorno.txt");
	  Future future = file.readAsString();
	  future.then((content) => faiQualcosaCon(contenuto))
	        .catchError((e) => gestisciErrore(e));
	}

Se `notizieDelGiorno.txt` non esiste oppure non è disponibile per la lettura, il codice precedente viene eseguito così:

1. Il Future di `readAsString()` si completa con un errore.
2. Il Future di `then()` si completa con un errore.
3. La funzione di callback di `catchError()` gestisce l'errore, il Future di `catchError()` si completa normalmente, e l'errore non si propaga.

> Concatenare `catchError()` a `then()` è una pratica molto comune quando si lavora con i Futures. Consideriamo la coppia `then-catchError` come l'equivalente asincrono dei blocchi `try-catch`.


Proprio come `then()`, `catchError()` restituisce un nuovo Future che si completa con il valore di ritorno della sua callback.

## Invocare più funzioni che restituiscono Futures

Consideriamo tre funzioni, `impegnativaA()`, `impegnativaB()` ed `impegnativaC()`, che restituiscono Futures. Queste funzioni possono essere invocate sequenzialmente (una funzione inizia quando la precedente si completa), oppure possono essere invocate contemporaneamente e si può fare qualcosa una volta che tutti i valori vengono restituiti. L'interfaccia di Future è fluida abbastanza da gestire entrambe le situazioni.

### Concatenare le chiamate alle funzioni usando then()

Quando le funzioni che restituiscono un Future devono essere eseguite in un certo ordine, usiamo le chiamate `then()` concatenate:

 impegnativaA().then((aValore) => impegnativaB()) 
            .then((bValore) => impegnativaC()) 
            .then((cValore) => faiQualcosaCon(cValore));


Funzionano anche le callback _nidificate_, ma sono più difficili da leggere, quindi poco _Dart-y_.

### Aspettare che tutti Futures si completino usando Future.wait()

Se l'ordine dell'esecuzione non è importante, si può usare `Future.wait()` per gestire più di un oggetto Future senza dover esplicitamente concatenare le funzioni.

Le funzioni vengono avviate in rapida successione; quando tutte sono complete con un valore, `Future.wait()` restituisce un nuovo Future. Questo Future si completa con una lista contenente i valori prodotti dalle diverse funzioni.

	Future.wait([impegnativaA(), impegnativaB(), impegnativaC()])
	      .then((List responsi) => scegliResponsoMigliore(responsi))
	      .catchError((e) => gestisciErrore(e));


Se ogni funzione invocata si completa con un errore, il Future restituito da `Future.wait()` si completa a sua volta con un errore che può essere gestito con `catchError()`

