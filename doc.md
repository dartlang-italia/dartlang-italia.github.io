---
layout: default
title: Panoramica del linguaggio
---

# {{ page.title }}


_Traduzione parziale della sezione [A Tour of the Dart Language](http://www.dartlang.org/docs/dart-up-and-running/contents/ch02.html) tratta dal sito ufficiale [dartlang.org](http://dartlang.org)._

---


## Un programma di base in Dart

Nel seguente programma sono presenti molte delle caratteristiche di base di Dart:

    // Definizione di una funzione.
    printNumber(num aNumber) {
      print('The number is $aNumber.'); // Stampa nella console
    }

    // Questo è il punto in cui parte l'applicazione all'avvio
    main() {
      var number = 42;           // Dichiara e inizializza una variabile
      printNumber(number);       // Invoca una funzione
    }


Gli elementi utilizzati in questo programma, comuni alla maggior parte delle
applicazioni in Dart:

`// Questo è un commento`

`num`: un tipo di dato. Altri tipi di dato disponibili sono _String, int_ e _bool_.

`42`: un numero nella forma letterale

`print()`: funzione built-in per stampare nella console

`'...'` e `"..."`: una stringa in forma letterale

`$variabile` oppure `${espressione}`: 


---

## Commenti

Esistono tre tipi di commenti:

1. Su una sola riga
    
    Il commento si apre con **//**. Al momento della compilazione, il contenuto del commento sarà ignorato.

        void main(){
            // Il programma inizia qui
            print('Hello world');
        }

2. Su più righe

    Il commento si apre con **/\*** e si chiude con **\*/**. Anche in questo caso il contenuto del commento sarà ignorato durante la compilazione.

        class Punto{
            num x;
            num y;
            /*
            'x' proiezione sull'asse delle ascisse
            'y' ^^^^^ ordinate
            */
        }
3. Documentazione

    Questo tipo di commento si apre con **/\*\*** e si chiude con **\*/**.
    A differenza degli altri tipi di commento, qui il testo viene ignorato solo se non è racchiuso fra parentesi quadre. Usando le parentesi quadre si possono incorporare collegamenti a classi, metodi e campi.


    I nomi fra parentesi quadre si riferiscono alla visibilità (scope) dell'elemento che si sta documentando. 

    Di seguito un esempio di commenti di tipo "Documentazione" con riferimenti ad altre classi e argomenti

        /**
         * Il lama (Lama glama) è un camelide addomesticato sudamericano
         */
        class Lama {
          String nome;

          /**
           * Dà da mangiare al lama [Cibo]. Una balla di fieno a settimana.
           */
          void alimenta(Cibo cibo) {
            // ...
          }

          /**
           * Esercita il tuo lama con [Attivita] per [timeLimit] minuti.
           */
          void esercitare(Attivita attivita, int timeLimit) {
            // ...
          }
        }



    Si usa l'utility `dartdoc`, inclusa nel SDK, per analizzare il codice Dart e generare la documentazione in HTML.


    Un esempio dell'output di `dartdoc`:

        <div class="doc">
            <p>Dà da mangiare al lama <a class="crossref" href="../lama/Cibo.html">Cibo</a>. Una balla di fieno a settimana.</p>
            <pre class="source">
                void alimenta(Cibo cibo) {
                }
            </pre>
        </div>

     `[Cibo]` è stato convertito in link HTML alle API docs per la classe Cibo.




## Variabili


Ecco un esempio su come creare una variabile e valorizzarla:

    var nome = 'Bob';

Le variabili sono dei puntatori. La variabile _nome_ punta ad uno _String object_ con valore "Bob"

###Valori predefiniti
Le variabili non inizializzate hanno _null_ come valore predefinito. Questo include i numeri, anch'essi oggetti.

    int contatore;
    assert(contatore == null); // Tutte le variabili sono inizialmente nulle.


###Tipi opzionali
Si possono aggiungere ad una variabile informazioni relative al tipo di dato in essa contenuto:

    String name = 'Bob';

Aggiungere il tipo è un modo per esprimere chiaramente il tuo intento.
Grazie alle notazioni relative al tipo di dato, il compilatore può fornire informazioni più precise 
e il Dart Editor può fornire suggerimenti più specifici.

###final
Se la variabile non deve essere modificata, si usa _final_ anzichè _var_, oppure lo si aggiunge al tipo. Se ad una variabile _final_ viene assegnato un valore, questo non potrà essere cambiato.

    final name = 'Bob'; // Oppure: final String name = 'Bob';
    name = 'Alice';     // ERRORE


---


## Tipi di dato

In Dart abbiamo i seguenti tipi di oggetto:

- _String_, una stringa di testo
- _num, int, double_, numeri
- _bool_, valori booleani
- _List_, liste ordinate di elementi (conosciute anche come _array_)
- _Map_, oggetti che associano un valore ad una chiave


Gli oggetti possono essere inizializzati utilizzando la notazione _letterale_, ovvero:

    String a = "Questa è una stringa";
    bool check = false;
    int x = 42;


Considerato che in Dart le variabili puntano ad un oggetto, ovvero ad una istanza di una classe,
per inizializzare una variabile si può usare un costruttore, come in questo esempio:

    Map m = new Map()

## Stringhe

Una stringa è una sequenza di caratteri Unicode racchiusa fra apici singoli o doppi:
    
    var s1 = 'Una prima stringa';
    var s2 = "Una seconda stringa";
    var s3 = 'Un\'altra stringa (occhio all\'escape)';
    var s4 = "Un'altra stringa";
    
Una stringa può includere il valore di un'espressione o il valore di una variabile, la sintassi nei due casi è la seguente:

_Le variabili hanno prefisso `$`, le espressioni sono contenute fra `${}`_

    var s1 = 'valore della variabile s1';
    var s2 = 'Questa stringa include il $s1';
    var s3 = 'QUESTA STRINGA INCLUDE IL ${s1.toUpperCase()}';
    
Per impedire che un elemento preceduto da `$` sia interpretato come variabile o espressione si può procedere in due modi:

1. Escape di `$`:

        var s1 = 'valore';
        var s2 = '\$s1';
        // il valore di s2 sarà "$s1"

2. Prefisso `@`:

        var s1 = 'valore';
        var s2 = @'$s1';
        // il valore di s2 sarà "$s1"
    


Due o più stringhe possono essere concatenate affiancandole:

    var s = 'La ''concatenazione '
        "non tiene conto dei ritorni a capo.";
        


#### Metodi

Gli oggetti `String` hanno metodi che possono tornare molto utili e qui ne mostriamo alcuni, come ad esempio `startsWidth()`, `endsWith()`, `contains()`: 

    var s = "Cantami, o Diva, del Pelìde Achille l'ira funesta";
    print(s.startsWith('Canta')) // stampa 'true'
    print(s.endsWith('Achille')) // stampa 'false'
    print(s.contains(new RegExp('Achille'))) // stampa 'true'
    
Il metodo `replaceAll()` sostituisce tutte le occorrenze in una data stringa:

    var templateSaluto = 'Ciao NOME!';
    var saluto = templateSaluto.replaceAll(new RegExp("NOME"), 'Federica');
    /*
    il contenuto di 'saluto' sarà 'Ciao Federica!'.
    il contenuto di 'templateSaluto' non sarà modificato.
    */
    
    
## Numeri

Esistono due `tipi` di numeri in Dart:

1. `int` per i numeri interi 

2. `double` per la rappresentazione a 64 bit di numeri a virgola mobile, secondo le specifiche _IEEE 754_
    
Entrambi `int` e `double` sono sotto-interfacce di `num`. L'interfaccia `num` include i quattro operatori di base (+,-,*,/) e gli operatori bitwise tipo _>>_. 

    var x = 1;
    var hex = 0xDEADBEEF;
    var bigInt = 3465346583465243765923847659234765928347659567398475647495873984572947593470294387093493456870849216348723763945678236420938467345762304958724596873045876234572037862934765294365243652548673456705673465273465246734506873456729457623845623456234650457693475603768922346728346256;
    
Se un numero dichiarato genericamente contiene cifre decimali, sarà automaticamente considerato di tipo `double`:

    var x = 1.1;
    var esponenziale = 1.42e5;
    
Le conversioni stringa/numero e viceversa funzionano così:

    import 'dart:math'; // la libreria dart:math è necessaria perché definisce i metodi che stiamo per utilizzare

    // String -> int
    var uno = int.parse("1");
    assert(uno == 1);

    // String -> double
    var unoVirgolaUno = double.parse("1.1");
    assert(unoVirgolaUno == 1.1);

    // int -> String
    var unoComeStringa = 1.toString();
    assert(unoComeStringa == "1");

    // double -> String
    var piComeStringa = 3.14159.toStringAsFixed(2);
    assert(piComeStringa == "3.14");

Operazioni di _bit shifting_:

    assert((3 << 1) == 6);  // 0011 << 1 == 0110
    assert((3 >> 1) == 1);  // 0011 >> 1 == 0001
    assert((3 | 4)  == 7);  // 0011 | 0100 == 0111

## Boolean

A differenza di Javascript che interpreta _1_ o qualsiasi oggetto non nullo come **true**, Dart intepreterà come **false** qualsiasi valore diverso da **true**.

    void main(){
    
        var x = true;
        var y = 'blabla';
    
        if(x){
            print('x vero');
        }
        else{
            print('x falso');
        }

        if(y){
            print('y vero'); 
        }
        else{
            print('y falso');
        }

    }   
    
    
## Liste (_Arrays_)

Nel codice seguente viene presentata una panoramica delle operazioni più comuni relative alle liste:

    var lista = [1,2,3]; // definizione della lista
    
    print(lista.length); // stampa la lunghezza
    
    lista.add(4); // aggiunge un elemento alla fine della lista
    
    print(lista[2]==3); // 'vero', perché l'elemento di indice 2 è 3
    
    lista.removeRange(2,1); // rimuove il terzo valore, il primo argomento indica la posizione iniziale, il secondo il numero di elementi da rimuovere
    
    
    print(lista[2]==3); // 'falso' perché è stato rimosso il 3, quindi il secondo valore è 4

    // questo è il ciclo 'for' se si ha bisogno dell'index 
    for (var x = 0; x < lista.length; x++){
        print('$x: ${lista[x]}');
    }
    
    // questo è il ciclo 'for' che non tiene conto dell'index
    for (final x in lista) {
      print(x);
    }
    
    /* output:
    3
    true
    false
    0: 1
    1: 2
    2: 4
    1
    2
    4
    */
    
Per applicare una funzione ad ogni elemento della lista si può usare il metodo `forEach()`:

    var lista = [1,2,3];
    stampaElemento(elemento) => print(elemento);  // Definisce la funzione
    lista.forEach(stampaElemento);  // Chiama la funzione per ogni elemento

una forma _contratta_ del metodo precedente:

    var lista = [1,2,3];
    lista.forEach((elemento) => print(elemento));

## Mappe

In Dart una mappa è un oggetto che associa determinate a chiavi a determinati valori. Ci sono due modi per definire una mappa, _letteralmente_ o tramite l'interfaccia _Map_.

Il modo letterale:

    var dizItaEng = {
    // chiave   : valore
        "gatto" : "cat";
        "cane"  : "dog";
        "mucca" : "cow";
    }

L'interfaccia _Map_:
    
    var dizItaEng = new Map();
    dizItaEng['gatto'] = "cat";
    dizItaEng['cane'] = "dog";
    dizItaEng['mucca'] = "cow";
    
Come nelle _liste_, usiamo `.length` per ottenere il numero di coppie chiave/valore.

    print(dizItaEng.length); // 3
    

Per rimuovere una coppia da una mappa usiamo il metodo `remove()`:

    dizItaEng.remove('cane');
    
Per copiare una mappa usiamo il costruttore `Map.from()`

    var dizionario = new Map.from(dizItaEng);
    
### Iterazioni

Si usa il metodo `forEach()` per accedere sia alle chiavi che ai valori:

    dizItaEng.forEach((k,v) => print('$k : $v'));
    
Se abbiamo bisogno solo delle chiavi o solo dei valori usiamo `getKeys()` o `getValues()`. Entrambi i metodi ritornano un oggetto di tipo `Collection`.

    var valori = dizItaEng.getValues()
    var chiavi = dizItaEng.getKeys()
    
    valori.forEach((v) => print(v))


---

## Funzioni

Questo è un esempio di come si dichiara una funzione:

    void printNumero(num numero) {
        print('Il numero è $numero.');
    }

Nonostante sia consigliabile specificare nella funzione il _tipo_ dei valori che riceve in ingresso, non è obbligatorio

    printNumero(numero) {    // E' possibile ometterli
        print('Il numero è $numero.');
    }

Per funzioni che contengono solo un comando, è possibile usare questa sintassi abbreviata:

    printNumero(numero) => print('Il numero è $numero.');
    

Questa espressione `=> expr ;` è la formula abbreviata di `{ return expr ;}`. `Expr` indica la chiamata alla funzione `print()` di questa funzione.

> Tra la freccia (`=>`) e il punto e virgola (`;`) può starci uno e un solo comando. 


Una funzione può avere due tipi di parametri, obbligatori od opzionali. Sono specificati per primi quelli obbligatori, seguiti da quelli opzionali.

## Parametri opzionali

I parametri opzionali possono essere passati per nome o per posizione, ma non entrambi.

Entrambi i tipi di parametri opzionali possono avere i rispettivi valori di default.
I valori di default devono essere conosciuti già dal momento della compilazione. Se non sono definiti, assumeranno il valore `null`.

Se si vuole sapere se è stata chiamata la funzione con il parametro opzionale settato, è possibile usare la sintassi ?parametro.

    if (?device) {    // Ha valore true se è stata passata la variabile device.
        //... L'utente setta il valore. Fa qualcosa con quello...
    }

###Parametri opzionali passati per nome

Quando si chiama una funzione è possibile specificare un parametro utilizzando la sintassi `nomeParametro: valore`. Ecco un esempio:

    abilitaFlags(bold: true, nascosto: false);

Quando si definisce una funzione, si usa la sintassi `{parametro1, parametro2, …}` per specificare i parametri passati per nome.


    // Setta i flags [bold] e [nascosto] con i valori che vuoi richiedere.
    abilitaFlags({bool bold, bool nascosto}) {
        ...
    }

Si usano i due punti (:) per specificare il valore di default.

    /**
    * Setta i flags [bold] e [nascosto] con i valori che vuoi richiedere.
    * definendo false come caso di default.
    */
    abilitaFlags({bool bold: false, bool nascosto: false}) {
        //...
    }
    
    abilitaFlags(bold: true); //bold sarà true, nascosto false.


###Parametri opzionali passati per posizione

Inserendo dei parametri tra `[  ]` essi vengono definiti come parametri opzionali passati per posizione.

    String messaggio(String mittente, String msg, [String device]) {
        var result = '$mittente ha detto: $msg';
        if (device != null) {
            result = '$result con un $device';
        }
        return result;
    }

Questo è un esempio di un'invocazione alla funzione precedente senza specificare il parametro opzionale:

    assert(messaggio('Carlo', 'Ciao mondo!') == 'Carlo ha detto: Ciao mondo!');

Di seguito specifichiamo anche il parametro opzionale:

    assert(messaggio('Carlo', 'Ciao mondo!', 'segnale di fumo') == 'Carlo ha detto: Ciao mondo! con un segnale di fumo');

E' possibile utilizzare = per definire il valore di default:


    String messaggio(String mittente, String msg, [String device='piccione viaggiatore', String umore]) {
        var result = '$mittente ha detto: $msg';
        if (device != null) {
            result = '$result con un $device';
        }
        if (umore != null) {
            result = '$result (con un $umore umore)';
        }
        return result;
    }
    
    assert(messaggio('Carlo', 'Ciao mondo!') == 'Carlo ha detto: Ciao mondo! con un piccione viaggiatore');

##Funzioni come oggetti _first-class_

È possibile passare una funzione come parametro di un'altra funzione. Per esempio:

    printElement(element) {
        print(element);
    }

    var list = [1,2,3];
    list.forEach(printElement); // Passa la funzione printElement come parametro.`


È inoltre possibile assegnare a una variabile il contenuto di una funzione. Ad esempio:

    var capitalizza = (msg) => '!!! ${msg.toUpperCase()} !!!';
    assert(capitalizza('ciao') == '!!! CIAO !!!');

## _Lexical closures_


Le funzioni possono includere variabili definite in contesti circostanti ad esse. Nell'esempio successivo, la funzione `makeAdder()` prende la variabile `n` e la rende disponibile alla sua funzione di ritorno. 


    Function makeAdder(num n) {
        return (num i) => n + i;
    }
    
    main() {
        var add2 = makeAdder(2); // Crea una funzione che ritorna (2 + i).
        var add4 = makeAdder(4); // Crea una funzione che ritorna (4 + i).
        
        assert(add2(3) == 5);
        assert(add4(3) == 7);
    }

##Valori di ritorno

Tutte le funzioni ritornano un valore. Se questo valore non è specificato, sarà implicita l'istruzione:

    return null;
    
alla fine della funzione.

---


# Librerie e visibilità

Le direttive `import`, `part`, `part of` e `library` permettono di scrivere un codice di base modulare e riutilizzabile. Le librerie non solo rendono disponibili le API ma permettono anche di definire la visibilità del codice: gli identificatori che iniziano con un underscore (_) sono visibili solo all'interno della libreria stessa.

> NB: Ogni applicazione Dart è una libreria, anche se non usa esplicitamente la direttiva `library`.

Le librerie possono essere distribuite utilizzando i `packages`. Vedi : [pub: Il gestore dei pacchetti Dart](/articoli/Pub.html).

### Usare le librerie ###

Per specificare che il _namespace_ di una libreria sia utilizzato all'interno di un'altra libreria è necessario usare la direttiva `import`.

Ad esempio, le applicazioni web in Dart utilizzano la libreria [dart:html](http://api.dartlang.org/html.html), che può essere importata in questo modo:

    import 'dart:html';

L'unico argomento richiesto per una coretta importazione è l'URI della libreria. Per le librerie di base incluse nel linguaggio, l'URI adotta lo speciale schema `dart:`. Per le altre librerie, si può utilizzare il percorso del file o lo schema `package:`. Lo schema `package:` indica che la libreria è fornita da un gestore di pacchetti come il tool `pub`. Ad esempio:

    import 'dart:io';  
    import 'package:mylib/mylib.dart';  
    import 'package:utils/utils.dart';  

### Indicare un prefisso per una libreria 
Se si importano due o più librerie che hanno identificatori con nomi in comune, è possibile indicare un prefisso per una delle librerie o per tutte. Ad esempio, se `lib1` e `lib2` presentano entrambe una classe `Elemento`, è possibile evitare il conflitto, scrivendo il codice in questo modo:

    import 'package:lib1/lib1.dart';  
    import 'package:lib2/lib2.dart' as lib2;  
    //...  
    var elemento1 = new Elemento();      // Usa Elemento da lib1.  
    var elemento2 = new lib2.Elemento(); // Usa Elemento da lib2.  

### Importare una libreria in modo parziale

Se occorre usare solo una parte di una libreria, è possibile effettuare una importazione selettiva. Esempio:

    import 'package:lib1/lib1.dart' show uno, due; // Importa solo uno e due da lib1.  
    import 'package:lib2/lib2.dart' hide uno;      // Importa tutto TRANNE uno da lib2.

### Implementare le librerie
Per specidificare i file che sono inclusi nella libreria occorre usare la direttiva `part` e la direttiva `library` per indicare che un file è dichiarato come libreria.

#### Usare file multipli 
Che si usi o no la direttiva `library`, è possibile usare `part` per indicare i file che implementano la libreria stessa.

    // Senza la direttiva library; questo file definisce una libreria anonima.  
    part 'ball.dart';   // Parte della definizione di questa libreria è in ball.dart.  
    part 'util.dart';   // Un'altra parte è in util.dart.  

    import 'dart:html'; // Questa applicazione usa la libreria  HTML.  
    
    //...  
    
    main() {            // Utilizzando il metodo main() facciamo sì che questa sia un'applicazione (e anche una libreria).  
      //...  
    }  

#### Dichiarazione di una libreria
Per dichiarare esplicitamente una libreria, si usa la direttiva `library`. Esempio:

    library gioco;      // dichiaro che è una libreria.  
    
    part 'ball.dart';   // Parte della definizione di questa libreria è in ball.dart.  
    part 'util.dart';   // Un'altra parte è in util.dart.  
    
    import 'dart:html'; // Questa applicazione usa la libreria  HTML.   
    
    //...  
    
    main() {            // Potremmo sposare questo codice in un altro file di questa libreria.  
      //...  
    }

#### Associare un file ad una libreria
Per indicare che un file è parte di una specifica libreria si può usare `part` seguito da `of` (`part of`). L'uso di `part of` è opzionale, ma è di supporto ad alcuni strumenti, come l'editore Dart. Esempio sull'uso di `part` e `part of`:

    // In gioco.dart:  
    library gioco;  
     
    import 'dart:utf';  
    // ... Altre importazione vanno qui ...  
      
    part 'ball.dart';  
    part 'util.dart';  
      
    //...  
      
      
    // In ball.dart:  
    part of gioco;  
      
    // ... il codice va qui ...  
      
      
    // In util.dart:  
    part of gioco;  
      
    // ... il codice va qui ...  
    
#### Ri-esportare le librerie
E' possibile riorganizzare le librerie ri-esportandole in parte o completamente. Ad esempio, si potrebbe avere una grossa libreria che implementa una serie di librerie più piccole o creare una libreria che espone un sottoinsieme dei metodi di un'altra libreria.


    // In italian.dart:  
    library italian;  
    hello() => print('Buongiorno!');  
    goodbye() => print('Arrivederci!');  
      
    // In togo.dart:  
    library togo;  
    import 'italian.dart';  
    export 'italian.dart' show hello;  
      
    // In un altro file .dart:  
    import 'togo.dart';  
      
    void main() {  
        hello();   // stampa Buongiorno!  
        goodbye(); // chiamata di un metodo inesistente
    }
