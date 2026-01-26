# 1-D Frogger (Easy)

Länk till problemet på Kattis: <https://open.kattis.com/problems/1dfroggereasy>

## Problemet

Spelplanen är `n` på varandra följande rutor. Varje ruta innehåller *en siffra* som **inte** är noll.

Rutorna är indexerade från vänster till höger, index börjar på `1` och slutar med `n`.

Spelet startar med att man slumpar/slår en tärning med alla index som spelplanen består av. Av det får man ett index `s` som resultat och på det indexet placerar man "grodan", det är startpunkten.

Sedan slumpar/drar man ett kort från en bunt kort som innefattar alla de siffror som också finns någonstans i en ruta på spelplanen. Den resulterande siffran `m` är det "magiska numret".

Sedan applicerar man detta nedan om igen tills spelet är slut:

* Om grodan är på en ruta med en positiv siffra `k` ska den "hoppa" `k` antal rutor till höger
* Om grodan är på en ruta med en negativ siffra `k` ska den "hoppa" `k` antal rutor till vänster

Spelet är slut när något av nedan är sant (grodan mött sitt öde):

1. Grodan landar på en ruta där siffran är samma som det magiska numret `m`. Även om det är den första ruta grodan landar på
2. Grodan "hoppar ut över kanten" längst till vänster på spelplanen
3. Grodan "hoppar ut över kanten" längst till höger på spelplanen
4. Grodan landar på en ruta där den varit tidigare och alltså har fastnat i en cykel/loop

**Input:**

* `n` = antalet rutor spelplanen består av
* `s` = index för den ruta där grodan startar
* `m` = magiska numret
* En rad med `n` siffror (ej `0`) skilda med mellanslag = var siffra som ska vara i var ruta från vänster till höger

**Output:**

* `h` = antal hopp grodan gör innan den möter sitt öde (spelet är slut)
* Något av orden `{"magic", "left", "right", "cycle"}` = grodans öde

**Begränsningar/överväganden:**

* `m` kan finnas i flera rutor på spelplanen
* `m` är garanterat att vara en siffra som finns bland de på spelplanen
* När en siffra `k` är negativ är det absolutvärdet av `k` som grodan hoppar
* `k` är inom intervallet `[-200, 200]`
* Antalet rutor kan vara från `1` stycken upp till `200`
* Index för den ruta där grodan startar `s` är aldrig större än antalet rutor `n`
* Index för den ruta där grodan startar `s` är som minst `1` (indexeringen startar alltså **inte** på `0`)

## Resonemang kring lösning

### Datastruktur

Eftersom att grodan kommer hoppa fram och tillbaka över hela spelplanen, i mitten, början och slutet, kommer jag behöva random access. En array är lämplig för detta, en array har O(1) i running time vid läsning [Grokking algoritms Ed. 2, kap 2] för att elementen ligger direkt bredvid varandra i minnet och det krävs en operation för att direkt hitta ett index.

### Representera grodans placering

I en variabel som sparar nuvarande index.

Dock behöver tidigare index också sparas då grodan inte får landa på en ruta där den varit tidigare (öde nr 4), då har den hamnat i en cykel/loop. *<- Fundering: handlar detta om det precis föregående indexet? Eller **alla** föregående index?*

Upptäckte en *sample input* som visar ödet `cycle`:

```java
8 2 13
7 5 4 2 13 -2 -3 6
```
