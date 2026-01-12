# Turn It Up

Länk till problemet på Kattis: <https://open.kattis.com/problems/skruop>

## Förstå problemet

Varje gång något ber om att skruva upp respektive ner så skruvas volymen upp eller ner med 1 steg.

11 steg på volymen (0-10). 7 är startvolym.

När volymen är 0 går det **inte** att flytta 1 steg **ner** längre, bara upp. Därför har förfrågan om att skruva ner ingen effekt i detta läge. Förfrågan om att skruva upp har fortfarande effekt.

När volymen är 10 går det **inte** att flytta 1 steg **upp** längre, bara ner. Därför har förfrågan om att skruva upp ingen effekt i detta läge. Förfrågan om att skruva ner har fortfarende effekt.

**Input:**

* `n` = the number of requests
* Flera rader med strängar som är antingen `Skru op!` eller `Skru ned!`

**Output:**

* En integer = den nuvarande volymen efter alla `n` requests gjorts

**Considerations/constraints:**

* Det kan komma från 1 upp till 100 rader stängar som input
* Volymen kan behöva ändras (flytta 1 steg) från 1 gång upp till 100 gånger
* Antalet requests `n` behöver inte överensstämma med antalet gånger volymen ändras (flyttas 1 steg) eftersom att vid extremiteterna 0 och 10 har inte alltid förfrågan effekt *<- Fundering: eftersom det finns två lägen på en request (upp/ner) innebär det att vid extemiteterna är det 50 % chans att volymen ska flyttas 1 steg? Sannolikheten att nästa request är upp är 50 % överallt på volymstegen, samma för ner. På alla volymsteg förutom 0 och 10 är sannolikheten att volymen ska flyttas 1 steg 100 % - eller är det? för antalet requests `n` kan ju också vara slut, dock har jag i denna uppgift i förväg antalet requests. Men är det vid extremiteterna 1/3 chans att volymen ska flyttas 1 steg? Dvs. vid 0 kan det bli upp (flyttas), ner (flyttas ej), slut på requests (flyttas ej)*

## Resonemang fram till lösning

### Lösningsidé 1

* Jag behöver en datastruktur som är effektiv att gå igenom sekventiellt från start till slut
* Kan jag göra om stängarna till int eller boolean direkt när jag läser in dem? För att göra höj/skänk effektivare än att jämföra stängar?

Skapa en array som är lika stor som `n`

Läs varje rad från input. Om det är `Skru op!` lägg till `1` i arrayen. Om det är `Skru ned!` lägg `-1` i arrayen

Väljer en array för att det är primitiva värden jag sparar i den samt för att jag vet redan hur många element jag har och det kommer inte ändras

Jag har min startvolym som är `7` i en variabel, säg `volume`. Jag loopar genom arrayen och lägger till värdet på aktuella indexet till `volume`, i och med att `+ -` blir `-` så kan jag direkt använda värdena i arrayen för att subtrahera `1` från volymen när request är **ner** och addera `1` till volymen när request är **upp**. Testat med:

```java
int start = 7;
int[] arr = {1, 1, -1, 1};
for (int e : arr) {
    start += e;
}
System.out.println(start); // 9
```

I loopen behöver jag varje varv kolla om `volume` är `0` eller `10`, och beroende på vilket kolla om värdet på indexet är `1` eller `-1`, och i så fall hoppa över adderingen

### Lösningsidé 2

* Kan jag ha användning av antalet steg (11) på volymstegen?
* Kan jag istället för att representera `Skru op!` och `Skru ned!` binärt, istället slå ihop antalet av samma förfrågan efter varandra till ett värde? Sekvensen skulle bli kortare då *<- Negativt: hur representerar jag då huruvida det aggregerade värdet innebär upp eller ner*
* Kan jag strunta i värdet `n`? Om jag räknar samman lika på varandra följande requests i sekvensen (punkten ovan) så stämmer inte antalet requests `n` överens med storleken på datastrukturen
* Kan jag ha en datastruktur som växer och sjunker för att representera volymförändringen? Är det snabbare än att uppdatera en variabel `volume` för varje request?

Jag kan använda en LIFO stack som datastruktur (<https://dev.java/learn/api/collections-framework/stacks-queues>). Jag vill lägga till element om request är **upp** och ta bort element om request är **ner**. "Last In, First Out" stämmer på detta problem för att om jag ska sänka volymen ska det senast tillagda elementet tas bort, därför passar en stack snarare än en queue för problemet.
