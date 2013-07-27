---
layout: default
title: JSON
autore: Gianluca Di Fonzo
descrizione: Manipolare oggetti JSON con Dart
autoreoriginale: Dart Team
titolooriginale: dart&#58;json - Encoding and Decoding Objects
urloriginale: https://www.dartlang.org/docs/dart-up-and-running/contents/ch03.html#ch03-json
---

JSON è un semplice formato di testo per la rappresentazione di oggetti e collezioni
strutturate. La libreria JSON decodifica stringhe JSON in oggetti Dart
e codifica oggetti in stringhe JSON.

La libreria Dart JSON funziona sia su applicazioni web che su applicazioni
da riga di comando. Per usare la libreria JSON bisogna importare `dart:json`.

##Decodificare una stringa JSON

Si decodifica una stringa JSON in un oggetto Dart con JSON.parse().


	import 'dart:json' as JSON;

	main() {
		// NOTA: Assicurarsi di usare doppi apici ("), e non apostrofi ('),
		// all'interno delle stringhe JSON. Questa stringa è JSON, non Dart
		var jsonString = '''
		[
			{"punteggio": 40},
			{"punteggio": 80}
		]
		''';

		var punteggi = JSON.parse(jsonString);
		assert(punteggi is List);

		var primoPunteggio = punteggi[0];
		assert(primoPunteggio is Map);
		assert(primoPunteggio['punteggio'] == 40);
	}

##Codificare una stringa JSON

Un oggetto Dart si codifica in una stringa JSON con il metodo `stringify()`.

Solo oggetti di tipo _int_, _double_, _String_, _bool_, _null_, _List_ o _Map_
possono essere codificati in JSON. Oggetti di tipo _List_ e _Map_ sono
codificati ricorsivamente.

Se un oggetto che non è dei suddetti tipi viene passato a `stringify()`,
allora viene chiamato il metodo `toJson()` dell'oggetto. Se `toJson()`
ritorna un valore codificabile, allora quel valora viene codificato al posto dell'oggetto.

	import 'dart:json';

	main() {
	var punteggi = [
		{'punteggio': 40},
		{'punteggio': 80},
		{'punteggio': 100, 'overtime': true, 'special_guest': null}
	];

	var jsonText = JSON.stringify(punteggi);
	assert(jsonText == '[
	  {"punteggio":40},
	  {"punteggio":80},'
	'{"punteggio":100,"overtime":true,'
	'"special_guest":null}]');
}