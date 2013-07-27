---
layout: default
title: Mixins
autore: Gianluca Di Fonzo
descrizione: Questo articolo descrive i mixin in Dart. Le semantiche sono deliberatamente ristrette in molti modi, in modo da ridurre conflitti con le implementazioni già presenti, permettendo tuttavia un'evoluzione futura verso un'implementazione matura dei mixin. Questa versione ristretta ne fornisce già un considerevole valore.
autoreoriginale: Gilad Bracha
titolooriginale: Mixins in Dart
urloriginale: http://www.dartlang.org/articles/mixins/
---

#Mixin in Dart

Questo articolo descrive i mixin in Dart. Le semantiche sono deliberatamente ristrette in molti modi, in modo da ridurre conflitti con le implementazioni già presenti, permettendo tuttavia un'evoluzione futura verso un'implementazione matura dei mixin. Questa versione ristretta ne fornisce già un considerevole valore.

##Concetti di base

Se hai familiarità con la letteratura accademica sui mixin allora puoi saltare questa sezione. Altrimenti, ne è consigliata la lettura, in quanto definisce concetti importanti e notazione dei mixin. Se si vuole approfondire l'argomento si può iniziare con questo articolo: [Mixins in Strongtalk](http://www.bracha.org/mixins-paper.pdf).

In un linguaggio che supporta classi ed ereditarietà, una classe definisce implicitamente un _mixin_. Il mixin è solitamente implicito - viene definito dal corpo della classe, e costituisce la differenza tra classe e la sua superclasse. La classe è di fatto una _applicazione di un mixin_ - il risultato dell'applicazione del suo mixin implicitamente definito alla sua sua superclasse.

Il termine _applicazione di un mixin_ viene da un'analogia con _applicazione di una funzione_. Matematicamente, un mixin M può essere visto come una funzione da una superclasse a una sottoclasse: passa a M una superclasse S, e viene restituita una nuova sottoclasse di S. Questo viene spesso scritto in letteratura con _M \|> S_.

Basandosi sulla nozione di applicazione di una funzione, si può definire la composizione di funzioni. Il concetto porta alla composizione di mixin; possiamo definire la composizione dei mixin _M<sub>1</sub>_ e _M<sub>2</sub>_, scritta come _M<sub>1</sub>_ * _M<sub>2</sub>_, come: _(M<sub>1</sub> * M<sub>2</sub>) \|> S = M<sub>1</sub> \|> (M<sub>2</sub> \|> S)_.

Le funzioni sono utili perché possono applicate a parametri differenti. Anche i mixin. Il mixin definito implicitamente da una classe viene solitamente applicato una sola volta alla superclasse data nella dichiarazione della classe. Per permettere che i mixin siano applicati a differenti superclassi, dobbiamo essere in grado di dichiarare i mixin indipendentemente da qualsiasi superclasse particolare, o in alternativa, di districare il mixin implicito di una classe e riusarlo all'infuori della sua dichiarazione originale. Questo è ciò che proponiamo di fare qui sotto.

##Sintassi e semantiche

I mixin sono implicitamente definiti mediante ordinarie dichiarazioni di classi. In linea di princippio, ogni classe definisce un mixin che può essere estratto da essa. Comunque, in questa proposta, un mixin può essere estratto solo da una classe che obbedisce alle seguenti resitrizioni:

1. La classe non ha costruttori dichiarati.
2. La superclasse della classe è Object.
3. La classe non contiene chiamate a `super`.

La restrizione (1) evita complicazioni che emergono dalla necessità di passare i parametri del costruttore sulla catena di ereditarietà. In queste circostanze, la restrizione (2) incoraggia la dichiarazione esplicita dei mixin. La restrizione (3) significa che le implementazioni possono continuare a legare staticamante le chiamate a `super` invece di rilegarle sulle mixin application, o legandole dinamicamente.

Esempio 1:

	abstract class Collection<E> {
		Collection<E> newInstance();
		Collection<E> map(f) {
		var result = newInstance();
		forEach((E e){result.add(f(e));})
		return result;
		}
	}

	typedef DOMElementList<E> = abstract DOMList with Collection<E>;
	
	typedef DOMElementSet<E> = abstract DOMSet with Collection<E>;


Qui, Collection<E> è una classe normale che viene usata per dichiarare un mixin. Entrambe le classi DOMElementlist e DOMElementSet sono applicazioni di un mixin. Vengono definite dalla dichiarazione typedef che le dà un nome e le dichiara uguali all'applicazione di un mixin a una superclasse, data attraverso una clausola `with`. La classe è astratta perché non implementa il metodo astratto newInstrance() dichiarato in Collection.

Come detto, DOMElementList è effettivamente _Collection mixin \|> DOMList_, mentre DOMElementSet è _Collection mixin \|> DOMSet_.

Il beneficio qui è che il codice nella classe Collection può essere condiviso in più gerarchie di classi. Abbiamo elencato sopra due di queste gerarchie - una radicata in DOMLista e una radicata in DOMSet. Non si ha bisogno di ripetere/copiare il codice in Collection, e ogni modifica effettuata a Collection sarà propagata in entrambe le gerarchie, facilitando di molto la manutenibilità del codice. L'esempio particolare è liberamente basato su un caso reale e veramente acuto nelle librerie Dart.

L'esempio di sopra illustra una forma di mixin application, dove l'applicazione specifica un mixin e una superclasse alla quale viene applicata, e fornisce l'applicazione con un nome.

In una forma alternativa, le mixin application appaiono nella clausola `with` della dichiriazione di una classe sotto forma di una lista di identificatore separati dalla virgola. Tutti gli identificatori devono denotare una classe. In questa forma, più mixin sono composti e applicati alla superclasse nominata nella clausola `extends`, producendo una superclasse anonima. Prendendo di nuovo lo stesso esempio, avremmo:

	class DOMElementList<E> extends DOMList with Collection<E> {
		DOMElementList<E> newInstance() => new DOMElementList<E>();
	}
	
	class DOMElementSet<E> extends DOMSet with Collection<E> {
		DOMElementSet<E> newInstance() => new DOMElementSet<E>();
	}

In questo caso, DOMElementList non è l'applicazione _Collection mixin \|> DOMList_. Invece, è una nuova classe la cui superclasse è un'applicazione del tipo descritto. La situazione è analoga per DOMElementSet. Da notare che in entrambi i casi, il metodo astratto newInstance() viene sovrascritto con un'implementazione, quindi queste classi possono essere direttamente istanziate.

Consideriamo cosa succede se DOMList ha un costruttore non banale:

	class DOMElementList<E> extends DOMList with Collection<E> {
		DOMElementList<E> newInstance() => new DOMElementList<E>(0);
		DOMElementList(size): super(size);
	}

Ogni mixin ha il suo costruttore chiamato indipendentemente, e così fa anche la superclasse. Dato che un costruttore di un mixin non può essere dichiarato, la chiamata a esso può essere eliminata dalla sintassi; nell'implementazione sottostante, la chiamata può essere sempre inserita nella lista d'inizializzazione.

Il costruttore dovrebbe impostare i valori per ogni campo e per ogni parametro di tipo generico.

Questa regola ci assicura che questi esempi vengano eseguiti facilmente e inoltre generalizzano in maniera pulita una volta che viene sollevate la restrizione (1).

La seconda forma è conveniente perché permette di mescolare più mixin in una classe senza la necessità di introdurre varie dichiarazioni intermedie. Per esempio:

	class Person {
		String name;
		Person(this.name);
	}

	class Maestro extends Person with Musical, Aggressive, Demented {
		Maestro(name):super(name);
	}

Qui, la superclasse è la mixin application: _Demented mixin \|> Aggressive mixin \|> Musical mixin \|> Person_.

Assumiamo che solo Persona abbia un costruttore con parametri. Quindi _Musical mixin \|> Person_ eredita i costruttori di Person, e così via fino all'effettiva superclasse di Maestro, che è formata da una serie di mixin application.

In realtà, in questo esempio ci si aspetterebbe che Demented, Aggressive e Musical abbiano effettivamente delle proprietà interessanti che potrebbero richiedere di essere valorizzate.

##Possibili problemi

Dopo aver illustrato la proposta, discutiamo ora alcune aree sulle quali possono esserci dei problemi:

- Privacy
- Static
- Tipi

###Privacy

Una mixin application può essere dichiarata all'infuori della libreria che ha dichiarato la classe originale. Questo potrebbe non avere alcun effetto su chi può accedere ai membri dell'istanza della mixin application. L'accesso ai membri viene determinato in base alla libreria dove sono stati dichiarati originariamente, esattamente come nell'ereditarietà ordinaria. In realtà, non c'è bisogno di sollevare la questione, dato che segue dalla semantica della mixin application, che è determinata dalla semantica dell'ereditarietà nel linguaggio sottostante.

###Static

Si possono usare gli attributi e i metodi static della classe originaria attraverso la mixin application?
Di nuovo, la risposta (No) segue dalla semantica dell'ereditarietà. Gli attributi/metodi static non sono ereditati in Dart.

###Tipi

Qual è il tipo di un'istanza di una mixin application? In generale, è un sottotiipo della sua superclasse,e supporta ancche i metodi definiti nel mixin. Il nome stesso del mixin, comunque, denota il tipo della classe originale, che ha la sua superclasse e può non essere compatibile con una particolare mixin application.

Cosa si può dire delle interfacce supportate da una classe? Un mixin le supporta? In generale, no, dato che il supporto all'interfaccia potrebbe fare affidamento su una funzione ereditata. Questo implica che una mixin application deve dichiarare esplicitamente quali interfacce implementa.

__Possiamo tranquillamente ignorare questo problema per il tempo a venire__. A causa della restrizione (2), il tipo di un mixin non include membri aggiuntivi oltre a quelli dichiarati dal mixini o condivisi da altri oggetti. Anche se il mixin implementa delle interfacce, il mixin stesso deve implementare i metodi delle interfacce, e quindi è sicuro assumere che ogni mix nel mixin è un sottotipo del tipo completo denotato dal nome del mixin.

Comunque, quando la restrizione (2) viene rilassata, il problema compare.

Questo farebbe discutere sul definire i mixin come costruttori distinti, in modo che il nome del mixin denoterebbe un tipo stabile. Comunque, questo richiede della pre-pianificazione. Invece, potremmo scegliere di denotare il tipo del mixin di una classe C mediante un tipo speciale di espressione.

Anche i generic sono un problema. Se una classe ha dei tipi parametrizzati, il suo mixin ha necessariamente gli stessi tipi parametrizzati.

##Estensioni

Una domanda chiave è se questa proposta può essere chiaramente estesa quando si rilassano le restrizioni. L'implicazione del rilassamento della restrizione (2) è stata già discussa. La restrizione (3) richiede più sofisticazioni nell'implementazione: le chiamate a `super` devono comparire per legarsi dinamicante alla superclasse effettiva. Questo può essere ottenuta sia copiando i metodi che usano `super`, o rendendo le chiamate a `super` legate in ritardo.

La restrizione (1) è più complessa. A causa della sintassi di Dart, non c'è alcun modo per passare gli argomenti del costruttore su per la catena di ereditarietà come parte della clausola `with` (per esempio, come in Scala).

Un approccio comprensivo per indirizzare il problema è illustrato qui sotto attraverso una variazione sull'esempio del maestro pazzo.

	class Musical {
		final Instrument instrument;
		Musical(this.instrument);
	}
	
	class Aggressive {
		final String aggressionLevel;
		Aggressive(this.aggressionLevel);
	}
	
	class Demented {
		final disorder;
		Demented(this.disorder);
	}
	
	class Maestro extends Person with Musical, Aggressive, Demented {
		Maestro(name, disorder, degree, instrument) :
			Demented(disorder), Aggressive(degree), Musical(instrument), super(name);
	}

Il costruttore di Maestro incanala esplicitamente i vari parametri verso i mixin che sono utilizzati per definire le sue superclassi.

La regola data nella proposta ristretta viene ancora applicata: ogni mixin ha il suo costruttore chiamato indipendentemente, come fa la superclasse. Viene chiamata solo la parte del costruttore che opera sul mixin stesso. Se il mixin avesse avuto una superclasse, il supercostruttore non sarebbe stato eseguito.

Se non ci sono chiamiate al costruttore del mixin, una chiamata di default della forma M(), dome M è il nome del mixin, dovrebbe essere inserita nell'implementazione. Questo assicurerà l'esistenza la chiamata del suo costruttore di default (se esiste). Ovviamente, queste chiamate possono essere ottimizzate se il mixin non ha campi o costruttori. Quindi, sia il comportamento che le performance della proposta ristretta sono conservate.