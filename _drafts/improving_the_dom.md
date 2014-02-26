#Migliorare i DOM

Una delle più grandi opportunità offerte da un nuovo linguaggio è la possibilità di definire delle API più chiare per gli utenti. Quando si tratta di sviluppare su un browser, l'API è il DOM. Le API per i DOM di cui soffrono i Javascripter stanno cadendo sotto il peso del tempo e di zavorre tecnologiche come XML.

Con Dart, abbiamo la possibilità di buttar via molta di questa storia grazie al lavoro di [Alex Russell](http://infrequently.org/) e di [Erik Arvidsson](http://erik.eae.net/) che hanno aiutato gli sviluppatori del linguaggio a mostrare come i DOM avrebbero potuto sembrare se avessimo avuto una macchina del tempo. Diamo un'occhiata:

##Nomi più semplici

I cambiamenti più semplici sono stati quelli di pulire alcuni nomi fastidiosi. `HTMLElement` è diventato `Element` ed è stato rimosso `HTML` dalla maggior parte dei nomi quando ciò ha avuto senso. Invece di `childNodes` e `children` abbiamo `nodes` e `elements`. `ownerDocument` + diventato semplicemente `document`. Si è tentato di ottimizzare i nomi in maniera tale che le cose più utilizzate siano le più concise.

Quando è stata l'ultima che hai usato `XMLHttpRequest` per inviare effettivamente richieste XML? È ciò che si sono chiesti gli sviluppatori. Quindi hanno rinominato `XMLHttpRequest` in `HttpRequest`.

Si è fatto anche un uso più consistente delle lettere maiuscole nei nomi dei DOM. Prendiamo per esempio `XMLHttpRequest`. Perché non chiamarlo `XmlHttpRequest` o `XMLHTTPRequest`? In Dart, non c'è da chiederlo - tutti i nomi contenenti acronimi con più di 2 caratteri utilizzano sempre il camelCase per l'uso delle lettere maiuscole, come `SvgElement`.

##Meccanismo migliorato per le query

Un'area dove i DOM hanno un mucchio di funzioni è la ricerca degli elementi. I DOM oggigiorno hanno un sacco di metodi per ricercare oggetti:

	getElementsById()
	getElementsByTagName()
	getElementsByName()
	getElementsByClassName()
	querySelector()
	querySelectorAll()
	document.links
	document.images
	document.forms
	document.scripts
	formElement.elements
	selectElement.options

Tutto ciò si è accumulato prima che [jQuery](jquery.com) comparse sulla cima di una montagna per rivelarci chi *Potete trovare i nodi utilizzando i selettori CSS*. Con la Vera Via nelle mani, l'API è stata ridotta a soli due (!) metodi: `query` e `queryAll()`.

<table>
	<tr>
		<td>	
			<code>
				// Vecchia API:	
				elem.getElementById('foo');
				elem.getElementsByTagName('div');
				elem.getElementsByName('foo');
				elem.getElementsByClassName('foo');
				elem.querySelector('.foo .bar');
				elem.querySelectorAll('.foo .bar');
			</code>
		</td>
		<td>
			<code>
				// Nuova API:
				elem.query('#foo');
				elem.queryAll('div');
				elem.queryAll('[name="foo"]');
				elem.queryAll('.foo');
				elem.query('.foo .bar');
				elem.queryAll('.foo .bar');
			</code>
		</td>
	</tr>
</table>


L'idea è che se vuoi usare una libreria simile a jQuery, va bene, ma non *dovresti* farlo solo per rendere l'API più accettabile.

##Utilizzo di collezioni vere

In JavaScript, i tipi di collezione di DOM sono diversi dal tipo Array, che mette difficoltà agli utenti quando i metodo utilizzati su uno non sono disponibili sull'altro. Per Dart, la situazione è stata resa più chiara. Metodi come `elements`, `nodes` e `query()` che ritornano collezioni, lo fanno ritornando oggetti che implementano le interfacce di collezioni presenti nella libreria base di Dart. Essi *sono* normali liste, mappe e set di Dart, o sono interscambiabili tra di loro.

Sviluppare con i tipi di collezione offerti da Dart ci permette di disfarci di un sacco di metodi per casi speciali. Invece di avere tanti metodi su `Element` per manipolare gli attributi, si è fatto diventare `attributes` una mappa:

<table>
	<tr>
		<td>
			<code>
				// Vecchio:
				elem.hasAttribute('name');
				elem.getAttribute('name')
				elem.setAttribute('name', 'value');
				elem.removeAttribute('name');
			</code>
		</td>
		<td>
			<code>
				// Nuovo:
					elem.attributes.containsKey('name');
					elem.attributes['name'];
					elem.attributes['name'] = 'value';
					elem.attributes.remove('name');
			</code>
		</td>
	</tr>
</table>

Allo stesso modo, rendendo `nodes` ed `elements` collezioni ben supportate, possiamo scartare molti metodi su `Element` e `Node`:

<table>
	<tr>
		<td>
			<code>
				// Vecchio:
				elem.hasChildNodes();
				elem.firstChild();
				elem.appendChild(child);
			</code>
		</td>
		<td>
			<code>
				// Nuovo:
				elem.nodes.isEmpty();
				elem.nodes[0];
				elem.nodes.add(child);
			</code>
		</td>
	</tr>
</table>

##Utilizzo dei costruttori

Per poter creare nuove istanze di oggetti DOM, si è costretti a passare dai factory method nel document. Ma a noi piacciono i costruttori:

<table>
	<tr>
		<td>
			<code>
				// Vecchio:
				document.createElement('div');
			</code>
		</td>
		<td>
			<code>
				// Nuovo:
				new DivElement();
			</code>
		</td>
	</tr>
</table>

Oppure:

	TableElement table = new Element.html(
    '<table><tr><td>Hello <em>Dart!</em></table>');

I costruttori nominali di Dart fanno sì che sia più facile far apparire gli oggetti DOM in più modi differenti.

##Eventi

È stato migliorato il modo in cui i gestori degli eventi vengono legati. Il DOM ha 2 modi di lavorare con gli eventi. Il vecchio è modo è quello in cui si lega un unico handlet settando direttamente una delle proprietà `on___` sull'elemento. (Per ragioni misteriose, questi sono chiamati `allinlowercase` al contrario del resto del DOM.)

La maniera più moderna è quella di utilizzare `addEventListener()` e `removeEventListener()`. Ciò permette in maniera sicura più listener per lo stesso evento, che è una gran cosa, ma rischia di essere troppo verboso. Inoltre si identifica l'evento dal suo nome stringato, che può portare facilmente a degli errori e non va molto d'accordo col type system.

Tutto ciò è stato semplificato. Sono state rimosse tutte le proprietà `on___` da Element ed è stata creata una nuova classe ElementEvents. Per ogni tipo d'evento conoscito, c'è una proprietà in questa classe: `click`, `mouseDown`, etc. Ognuno di queste proprietà è un event object che può aggiungere e rimuovere listener e inviare eventi.

Il tutto è stato collegato al DOM dando a Element una proprietà `on` che vi ci accede. Ora tutte le funzionalità correlate agli eventi sono nascoste in una singola proprietà. Ecco un esempio:

<table>
	<tr>
		<td>
			<code>
				// Vecchio:
				elem.addEventListener('click',
				    (event) => print('click!'), false);
				
				elem.removeEventListener(
				    'click', listener);
			</code>
		</td>
		<td>
			<code>
				// Nuovo:
				elem.on.click.add(
				    (event) => print('click!'));
				
				elem.on.click.remove(listener);
			</code>
		</td>
	</tr>
</table>

Nella remota possibilità che tu voglia registrare un evento con un nome dinamico, o lavorare con eventi in maniera generica, è stato inserito un operatore subscript su ElementEvents:

<table>
	<tr>
		<td>
			<code>
				// Vecchio:
				elem.addEventListener(
				    'some' + 'name',
				    (event) => print('dynamic!'),
				    false);
			</code>
		</td>
		<td>
			<code>
				// Nuovo:
				elem.on['dynamic' + 'event' + 'name'].add(
				    (event) => print('dynamic!'));
			</code>
		</td>
	</tr>
</table>

Alcune classi DOM supportano più eventi di quelli presenti in Element. Per essi, ci sono delle sottoclassi di ElementEvents che aggiungono più proprietà. Per esempio, `on` di Document ritorna una classe DocumentEvents che aggiunge `readyStateChange`, `selectionChange` e `contentLoaded`.

##Librerie più granulari

Le librerie originali DOM risiedono tutte nello stesso namespace. Quindi WebGL e SVG condividono lo stesso spazio con elementi ed eventi. Questo ha forzato a effettuare il namespace sui nome delle classi e delle funzioni. Per esempio, tutti i nomi delle classi SVG iniziano per, guarda caso, "SVG".

Dart supporta librerie vere, quindi possiamo prendere grandi sezioni dell'interfaccia DOM assegnandone un appropriato namespace. Si è incominciato con `dart:svg`, che mette insieme tutte le classi SVG rimuovendo il prefisso SVG dai loro nomi. Questo rende `dart:html` più leggero.

Tra le altre librerie DOM così suddivise vi cono `dart:indexed_db` e `dart:web_audio`.

##Consistenza cross-browser nelle API HTML

Quando è stata l'ultima che hai scritto delle varianti per un browser specifico nel tuo codice in maniera tale che il tuo sito si comportasse più o meno allo stesso modo su tutti i browser? Probabilmente l'ultima volta che hai scritto codice web!

Dart si batte per fornire consistenza nel comportamento tra i browser in maniera tale che non bisogna preoccuparsi di questi dettagli. Nonostante ciò sia ancora work in progress, probabilmente ti preoccuperai di meno dei specifici browser mentre utilizzi le librerie HTML di Dart.

Un esempio è la classe `KeyboardEventController`, che fornisce un miglior supporto per gli eventi legati alla tastiera sulle varie tastiere internazionali.

##Per saperne di più

Si invita a leggere [dart:html API docs](http://api.dartlang.org/html.html), e a guardare i seguenti esempi, inclusi in [Dart Editor](http://www.dartlang.org/docs/editor/) e che usano la libreria dart:html: [Slider](http://code.google.com/p/dart/source/browse/trunk/dart/samples/slider/) e [Sunflower](http://code.google.com/p/dart/source/browse/trunk/dart/samples/sunflower/).

Parlando di programmazione per browser, potrebbero interesserati [le librerie Web UI che creano i componenti web e forniscono bindining tra i dati live e a 2 vie](http://www.dartlang.org/articles/dart-web-components/).