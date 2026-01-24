# Turn It Up

Länk till problemet på Kattis: <https://open.kattis.com/problems/skruop>

## Problemet

Varje gång någon ber om att skruva upp respektive ner så skruvas volymen upp eller ner med 1 steg.

11 steg på volymen (0-10). 7 är startvolym.

När volymen är 0 går det **inte** att flytta 1 steg **ner** längre, bara upp. Därför har förfrågan om att skruva ner ingen effekt i detta läge. Förfrågan om att skruva upp har fortfarande effekt.

När volymen är 10 går det **inte** att flytta 1 steg **upp** längre, bara ner. Därför har förfrågan om att skruva upp ingen effekt i detta läge. Förfrågan om att skruva ner har fortfarende effekt.

**Input:**

* `n` = the number of requests
* Därefter `n` rader som är antingen `Skru op!` eller `Skru ned!`

**Output:**

* Utskrift av en integer = volymen efter alla `n` requests gjorts

**Begränsningar/överväganden:**

* Det kan komma från 1 upp till 100 rader stängar som input
* Volymen kan behöva ändras (flytta 1 steg) från 1 gång upp till 100 gånger
* Antalet requests `n` behöver inte överensstämma med antalet gånger volymen ändras (flyttas 1 steg) eftersom att vid extremiteterna 0 och 10 har inte alltid förfrågan effekt

## Resonemang kring lösning

### Lösningsidé 1

* Jag behöver en datastruktur som är effektiv att gå igenom sekventiellt från start till slut
* Kan jag göra om strängarna till `int` eller `boolean` direkt när jag läser in dem, för att kunna höja/skänka effektivare?

Skapa en array som är lika stor som `n`

Läs varje rad från input. Om det är `Skru op!` lägg till `1` i arrayen. Om det är `Skru ned!` lägg till `-1` i arrayen

Väljer en array för att det är primitiva värden jag ska spara, jag ska loopa genom den sekventiellt samt för att antalet element är känt och antalet kommer inte ändras

Startvolymen `7` sparas i en variabel. Eftersom att `+ -` blir `-` så kan jag direkt använda värdena i arrayen för att subtrahera `1` från volymen när request är **ner** och addera `1` till volymen när request är **upp**. Till exempel `volume + 1` adderar `1` till `volume` medans `volume + (-1)` subtraherar `1` från `volume`

För varje element i arrayen behöver jag kolla om `volume` är `0` eller `10`, och beroende på vilket kolla om elementet är `1` eller `-1` och då hoppa över den requesten

### Lösningsidé 2

* Kan jag ha användning av antalet steg (11) på volymstegen?
* Kan jag istället för att representera `Skru op!` och `Skru ned!` som enskilda värden slå ihop antalet efter varandra följande `Skru op!` och `Skru ned!` i sekvensen. Sekvensen skulle bli kortare då med aggregerade värden *<- Fundering: hur representerar jag då huruvida det aggregerade värdet innebär upp eller ner?*
* Kan jag strunta i värdet `n`? Om jag ändå räknar samman lika på varandra följande requests i sekvensen (punkten ovan) får jag istället ett annat värde på hur många gånger volymen ändras
* Kan jag använda en datastruktur som växer och sjunker för att representera volymförändringen? Är det snabbare än att uppdatera en variabel `volume` för varje element i en array?

Jag kan använda en LIFO stack som datastruktur (<https://dev.java/learn/api/collections-framework/stacks-queues>). Jag vill lägga till element om request är **upp** och ta bort element om request är **ner**. "Last In, First Out" stämmer på detta problem för att om jag ska sänka volymen ska det senast tillagda elementet tas bort, därför passar en stack snarare än en queue för problemet

LinkedList kan användas som en stack. Jag behöver bara lägga till och ta bort element i början av listan, jag behöver inte iterera över den och jag behöver inte lägga till eller ta bort element från mitten av listan. Testat med:

```java
Deque<Integer> stack = new LinkedList<>(List.of(1, 1, 1, 1, 1, 1, 1));
stack.push(2);
System.out.println(stack); // [2, 1, 1, 1, 1, 1, 1, 1]
stack.pop();
System.out.println(stack); // [1, 1, 1, 1, 1, 1, 1]
stack.pop();
System.out.println(stack); // [1, 1, 1, 1, 1, 1]
```

För att veta när höjning/sänkning inte ska ha effekt (extremiteterna) kan jag beräkna om storleken på stacken subtraherat/adderat det aggregerade värdet fortfarande är `<10` och `>0`

Om jag skapar aggregerade värden av på varandra följande `Skru op!` och `Skru ned!` så kan jag representera dessa som positiva `int`s om `Skru op!` och negativa `int`s om `Skru ned!`. Så att en följd på 4x`Skru op!` lägger till värdet `4` i en array, 7x`Skru ned!` lägger till `-7` och 1x`Skru op!` lägger till `1`

Genom att aggregera requests på detta sätt kan jag kolla om en hel grupp av samma requests "når" en extremitet eller inte, jag behöver inte kolla om varje request gör det

## Lösning

Nedan har jag implementerat Lösningsidé 1. Den är minst komplicerad för vad det här problemet kräver.

```java
import java.util.Scanner;

class Skruop {
    public static void main(String[] args) {
        
        int[] requests = null;

        try (Scanner sc = new Scanner(System.in)) {
            int requestsTotal = sc.nextInt();
            sc.nextLine(); // Clear newline that was left after nextInt()
            requests = new int[requestsTotal];

            int index = 0;
            while (sc.hasNextLine()) {
                boolean isUp = sc.nextLine().equals("Skru op!") ? true : false;
                if (isUp)
                    requests[index] = 1;
                else
                    requests[index] = -1;
                index++;
            }
        } catch (Exception ex) {
            System.out.println("Catched Exception: " + ex.getMessage());
        }

        int volume = 7;
        for (int r : requests) {
            if ((volume == 0 && r == -1) || (volume == 10 && r == 1))
                continue;
            else
                volume += r;
        }
        
        System.out.print(volume);
    }
}
```
