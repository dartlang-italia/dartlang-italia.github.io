---
layout: default
title: dart:io
autore: Gianluca di Fonzo
descrizione: La libreria di Dart per gestire file, cartelle, processi, socket e connessioni HTTP
autoreoriginale: Mads Ager
titolooriginale: An Introduction to the dart:io Library
urloriginale: http://www.dartlang.org/articles/io/
---

La libreria dart:io fornisce l'API per utilizzare file, cartelle, processi, socket e connessioni HTTP. Solo le applicazioni da riga di comando possono usare dart:io - e non le web app.

In generale, la libreria dart:io implementa e supporta un'API asincrona. Metodi sincroni possono bloccare facilmente il loop dell'evento, rendendo difficile la realizzazione di applicazioni server scalabili. Quindi, la maggior parte delle operazioni ritorna risultati attraverso delle callback o mediante oggetti Future, una pattern comune nelle piattaforme server moderne come Node.js.

I pochi metodi sincroni nella libreria dart:io sono marcati chiaramente col suffisso Sync sul nome del metodo. I metodi sincroni non saranno discussi qui.


##File e cartelle

La libreria I/O permette alle applicazioni da riga di comando di leggere e scrivere file e navigare nelle cartelle. Ci sono due modi per leggere il contenuto di un file: tutto in una volta, o con uno stream.
Leggere il file tutto in una volta richiede memoria a sufficienza per memorizzare tutto il contenuto del file. Se il file è molto grande o lo si vuole elaborare durante la lettura, allora si dovrebbe utilizzare un `InputStream`, come descritto in *Stream di un file*.

###Lettura di un file di testo

Quando si legge un file di testo, si può leggerne l'intero contenuto con `readAsText()`.
Quando le singole linee sono importanti, si può usare `readAsLines()`.
In entrambi i casi, viene restituito un oggetto _Future_ che fornisce il contenuto del file come una o più stringhe.

	import 'dart:io';

	main() {
		var config = new File('config.txt');

		// Inserisce l'intero file in una singola stringa
		config.readAsText(Encoding.UTF_8).then((String contenuti) {
			print("L'intero file è lungo ${contentuti.length} caratteri");
		});

		// Inserisce ogni linea del file in stringhe dedicate
		config.readAsLines(Encoding.UTF_8).then((List<String> linee) {
			print("L'intero file è composto da${lines.length} linee");
		});
	}

###Lettura di un file binario

Il seguente codice legge un intero file binario e lo inserisce in una lista di interi.
La chiamata a `readAsBytes()` restituisce un Future, che fornisce il risultato quando è disponibile.

	import 'dart:io';

	main() {
		var config = new File('config.txt');

		config.readAsBytes().then((List<int> contenuti) {
			print("L'intero file è lungo ${contents.length} byte");
		});
	}

###Gestione degli errori

Gli errori vengono lanciati come eccezioni se non viene registrato un handler esplicito.
Se si vuole catturare un errore, si può registrare un gestore handleException con l'oggetto Future.

	main() {
		var config = new File('config.txt');
		Future readFile = config.readAsText();
		readFile.handleException((e) {
			print(e);
			// ...Qui vanno altre gestioni dell'errore...
			return true; // Abbiamo gestito l'eccezione; non serve propagarla.
		});
		readFile.then((text) => print(text));
	}

###Stream di un file

Per leggere un file un po' alla volta si utilizza un `InputStream`. La funzione di callback `onData`
viene eseguita quando i dati sono pronti per essere letti.
Quando l'InputStream ha terminato la lettura, allora viene eseguita la callback `onClosed`.

	import 'dart:io';

	main() {
		var config = new File('config.txt');
		var inputStream = config.openInputStream();

		inputStream.onError = (e) => print(e);
		inputStream.onClosed = () => print('file chiuso');
		inputStream.onData = () {
			List<int> bytes = inputStream.read();
			print('Letti ${bytes.length} byte dallo stream');
		};
	}

Per decodificare un `InputStream` da byte a caratteri, bisogna incapsulare
l'InputStream con un `StringInputStream`. Si possono leggere le stringhe sia
quanto i dati diventano disponibili sia una linea alla volta.

###Scrittura di un file
Per scrivere dati in un file si utilizza un `OutputStream`.
Per farlo, bisogna aprire il file in scrittura con `openOutputStream()` e
dichiarare una modalità di scrittura.
Aprendo il file in modalità `FileMode.WRITE` si sovrascrivono completamente i
dati presenti nel file, aprendolo con `FileMode.APPEND` i dati vengono aggiunti alla fine del file.

	import 'dart:io';

	main() {
		var logFile = new File('log.txt');
		var out = logFile.openOutputStream(FileMode.WRITE);
		out.writeString('FILE APERTO ${new Date.now()}');
		out.close();
	}

Per scrivere dati binari, si usa `write(List<int> buffer)`.

###Elencare i file di una cartella
Trovare tutti i file e sottocartelle di una cartella è un'operazione asincrona.
Il metodo `list()` ritorna un `DirectoryLister`, sul quale si possono registrare
i callback handler che devono essere notificati quando si incontra un file (usando `onFile`)
o quando si incontra una cartella (usando `onDir`).

	import 'dart:io';

	main() {
		var dir = new Directory('/tmp');

		DirectoryLister lister = dir.list(recursive:true); // Returns immediately.
		lister.onError = (e) => print(e);
		lister.onFile = (String name) => print('Found file $name');
		lister.onDir = (String name) => print('Found dir $name');
	}

###Altre funzionalità comuni

Le classi File e Directory contengono altre funzionalità, tra cui:

- Creare un file o una cartella: `create()` in File e Directory;

- Cancellare un file o una cartella: `delete()` in File e Directory;

- Ottenere la lunghezza di un file: `length()` in File;

- Ottenere l'accesso casuale a un file: `open()` in File.

La lista intere dei metodi è presente nella documentazione per File, Directory e DirectoryLister.
Si invinta a consultare anche la documentazione per InputStream, StringInputStream e OutputStream

In aggiunta all'API discusse in questa sezione, la libreria `dart:io` fornisce API per processi, socket e web socket.


La librerio `dart:io` fornisce le classi che le applicazioni da riga di comando
possono utilizzare per accedere alle risorse e ai server HTTP.

##HTTP server

La classe `HttpServer` fornisce la funzionalità a basso livello per costruire
dei web server. Si possono associare request handler, inmpostare gli header,
trasmettere dati, e altro.

Dato che Dart non è multithread e ha un event loop, la progettazione delle API di `HttpServer`
favoriscono le callback per la gestione degli eventi.

Il seguente web server di esempio può restituire solo semplice testo d'informazione.
Questo server ascolta sulla porta 8888 dell'indirizzo 127.0.0.1 (localhost),
rispondendo alle richieste per il path `/languages/dart`.
Tutte le altre richieste sono gestite dal request handler di default, che ritorna
il codice di risposta 404 (not found).

	import 'dart:io';

	main() {
		dartHandler(HttpRequest request, HttpResponse response) {
			print('Nuova richiesta');
			response.outputStream.writeString('Dart è opzionalmente tipizzato');
			response.outputStream.close();
		};

		var httpServer = new HttpServer();
		httpServer.addRequestHandler(
			(req) => req.path == '/languages/dart',
			dartHandler);

		httpServer.listen('127.0.0.1', 8888);
	}


###Client HTTP

La classe `HttpClient` aiuta a connettere alle risorse HTTP dall'applicazione Dart
lato server. Si possono impostare gli header, usare metodi HTTP, e leggere e scrivere dati.

Nota: La classe `HttpClient` non funziona nelle applicazioni browser-based.
Per programmare applicazioni browser-based, usa la classe `HttpRequest`.

L'API di `HttpClient`, come quella di `HttpServer`, è callback-oriented. Il flusso generale degli eventi è come segue:

1. Crea un nuovo `HttpClient`.

2. Ottiene l'URL.

3. Definisce la callback `onResponse()` sulla `HttpClientconnection`.

4. Definisce la callback `onData()` sull'input stream.

5. Definisce la callback `onClosed()` sulla `HttpClientConnection`.

6. Legge dati quando sono disponibili.

7. Chiude `HttpClient` quando non vuoi più creare delle connessioni che lo utilizzano.

		import 'dart:io';
		import 'dart:uri';

		main() {
			var httpClient = new HttpClient();
			var conn = httpClient.getUrl(new Uri('http://127.0.0.1:8888/languages/dart'));
			conn.onResponse = (HttpClientResponse resp) {
				var input = resp.inputStream;
				input.onData = () {
					var data = input.read();
					var text = new String.fromCharCodes(data);
					print(text);
				};
				input.onClosed = () => httpClient.shutdown();
			};
		}