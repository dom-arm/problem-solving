# 1-D Frogger (Easy)

Länk till problemet på Kattis: <https://open.kattis.com/problems/1dfroggereasy>

## Problemet

Spelplanen är `n` på varandra följande rutor. Varje ruta innehåller *en siffra* som **inte** är noll.

Rutorna är indexerade från vänster till höger, index börjar på `1` och slutar med `n`.

Spelet startar med att man slumpar/slår en tärning med alla index som spelplanen består av. Av det får man ett index `s` som resultat och det indexet är startpunkten där man placerar "grodan".

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
* `m` är garanterat att vara en siffra som finns på spelplanen
* När en siffra `k` är negativ är det absolutvärdet av `k` som grodan hoppar
* `k` är inom intervallet `[-200, 200]`
* Antalet rutor kan vara från `1` stycken upp till `200`
* Index för den ruta där grodan startar `s` är aldrig större än antalet rutor `n`
* Index för den ruta där grodan startar `s` är som minst `1` (indexeringen startar alltså **inte** på `0`)

## Resonemang kring lösning

### Datastruktur

Eftersom att jag kan behöva läsa värden från index lite här och där på spelplanen, behövs snabb random access. Därför använder jag en array.

### Göra ett hopp

Adderar siffran `k` med nuvarande index. Om `k` är negativ kommer ett vänsterhopp ändå representeras korrekt då `+ -` blir `-`

### Grodan fastnar i en cykel

Tidigare index behöver sparas då grodan inte får landa på en ruta där den varit tidigare (öde nr 4), då har den hamnat i en cykel/loop. *<- Fundering: handlar detta om det precis föregående indexet? Eller **alla** föregående index?*

En *sample input* från uppgiften visar ödet `cycle`:

```java
8 2 13
7 5 4 2 13 -2 -3 6
```

Jag ser att `2 13 -2` blir en oändlig cykel. Jag kan upptäcka dessa fall genom att testa om nuvarande siffra `k` adderat med nästkommande siffra `k` blir `0` (i så fall är det en cykel). *<- Notera: det går att upptäcka på detta sätt, men ett hopp måste registreras, för att med denna lösning kollar man "ett steg fram" och sedan avbryter, men då missar man att räkna hoppet som "skulle bli" till den rutan*

**Exempel:**

```java
-2 + 2 = 0
2 + -2 = 0
```

Alltså tänker jag att alla tidigare index inte behöver sparas utan man gör beräkningen mellan nuvarande ruta och rutan man ska hoppa till. *<- Notera: en cykel kunde visst uppträda med flera mellanliggande hopp, inte bara mellan två hopp*

### Grodan hoppar över en kant

För att beräkna detta behöver man nuvarande index, totala antalet rutor och siffran i den nuvarande rutan.

Sedan adderar man siffran till indexet och om det blir större än totala antalet rutor eller mindre än 1, så har grodan hoppat över vänster eller höger kant. *<- Notera: detta kollar också "ett steg fram" då jag adderar siffran till indexet. Istället kan jag bara kolla nuvarande index helt enkelt*

### Lösningsidé med rekursion

Jag påbörjade lösningen med att använda en while-loop, men gick över till en rekursiv metod.

Det var mer läsbart att lösa detta problem så, tyckte jag. Det är flera variabler som uppdateras under varje varv och istället för att initiera och uppdatera dessa utanför en while-loop så var det tydligare att skicka dessa som argument till den rekursiva metoden.

**Base case:**

Det magiska numret är på det första hoppet

**Rekursivt fall:**

Annars, det magiska numret är på nästa hopp

**Flöde:**

Ta emot arrayen, ett index och ett magiskt nummer

Om värdet på indexet är samma som det magiska numret, returnera `magic`

Annars, det magiska numret är på index + siffran på indexet (nästa index) *<- Notera: index out of bound blir ett problem*

**Exempel på flödet i kod:**

```java
String hop(int[] arr, int i, int m, int hops) {
    if (arr[i] == m) {
        return "magic " + hops;
    } else {
        return hop(arr, i + arr[i], m, hops + 1);
    }
}
```

Sedan addera resterande öden som villkor.

## Lösning

```java
import java.util.Arrays;
import java.util.HashSet;
import java.util.Scanner;

class Frogger1dEasy {
    public static void main(String [] args) {
        Scanner sc = new Scanner(System.in);
        String[] startVars = sc.nextLine().split("\s"); // n s m
        int[] board = Arrays.stream(sc.nextLine().split("\s")).mapToInt(Integer::parseInt).toArray();
        sc.close();

        int startIndex = Integer.parseInt(startVars[1]) - 1; // The board is not 0-indexed, but the array representing
                                                             // it is, so I subtract 1 from the start index
        int magicNumber = Integer.parseInt(startVars[2]);

        String[] result = hop(board, startIndex, magicNumber, new HashSet<Integer>(), 0).split("\s");
        System.out.println(result[0]); // fate
        System.out.println(result[1]); // hops
    }

    static String hop(int[] board, int i, int m, HashSet<Integer> prev, int hops) {
        if (i >= board.length) {
            return "right " + hops;
        } else if (i < 0) {
            return "left " + hops;
        } else if (board[i] == m) {
            return "magic " + hops;
        } else if (prev.contains(i)) {
            return "cycle " + hops;
        } else {
            prev.add(i);
            return hop(board, i + board[i], m, prev, hops + 1);
        }
    }
}
```

## Utvärdering

Min initiala lösning för att upptäcka en cykel täckte inte alla testfall, rekursionen gick in i en oändlig loop. Jag förstod då att en cykel inte bara sker mellan två hopp, utan kan ske med flera mellanliggande hopp. Mitt villkor `if (i + nextIndex == 0)` var alltså inte tillräckligt. Jag valde då att lägga till ett HashSet som argument för att spara tidigare index, till vilket jag la till `i` innan jag gjorde nästa rekursiva anrop.

Jag fick `ArrayIndexOutOfBoundsException` för att mitt villkor `if (i > board.length)` inte fångade när `i` var exakt lika med arrayens längd. Till exempel, om `i` är `6` och `board.length` är `6` blir villkoret falskt, flödet fortsätter och vid nästa `board[i]` ger det ett exception för att uttrycket blir `board[6]`, men arrayens index är `0-5`. Korrekt villkor ska vara `if (i >= board.length)`.

I den slutgiltiga lösningen har det betydelse att villkoren för att upptäcka om grodan hoppar över en kant, kommer först, och i dessa fall returnera direkt.
