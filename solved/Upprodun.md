# Uppröðun

Länk till problemet på Kattis: <https://open.kattis.com/problems/upprodun>

## Förstå problemet

Det finns `N` klassrum och `M` tävlande.

Till varje klassrum ska de tävlande fördelas så att det så långt som möjligt är lika många tävlande i varje klassrum. *<- Första uppfattnig, feltolkat*

* Är det 8 tävlande och 3 klassrum ska fördelningen vara 3, 3, 2
* Är det 22 tävlande och 5 klassrum är fördelningen 5, 5, 5, 5, 2 **INTE BRA**
* Fördelningen 5, 5, 5, 4, 3 är heller **INTE BRA**
* Fördelningen 4, 4, 4, 5, 5 är **BRA**

Ny korrekt tolkning: Till varje klassrum ska de tävlande fördelas så att antal tävlande `k` i varje klassrum är så nära nummer som möjligt.

Problemet handlar om tävlande i "team" och därför bör det inte kunna finnas ett klassrum med 1 tävlande, men detta framgår dock inte i uppgiften. Om det är 5 tävlande och 3 klassrum bör fördelningen vara 3, 2, 0 eftersom det handlar om "team". Men om man ser till de constraints som anges i uppgiften och uppgiftens beskrivning så ska det vara 2, 2, 1.

**Constraints:**

* Antalet tävlande `M` kan vara färre än antal klassrum `N`
* Det kan vara exakt ett klassrum `N`
* Det kan vara exakt en tävlande `M`
* Det kan vara max 10 klassrum och max 500 tävlande
* Antalet klassrum och antalet tävlande kan vara 0 eller mindre

**Input:**

* `N` = classrooms
* `M` = contestants

**Output:**

* `N` lines
* `i` = a classroom (a line)
* `k` = contestants in a classroom (represented as `*`'s)

## Resonemang fram till lösning

Om antal klassrum är 0 eller mindre, i så fall innebär det **inga** lines att skriva ut, hoppa alltså över, ingen beräkning behövs

Om antal tävlande är 0 eller mindre, i så fall ska `N` *tomma* lines direkt skrivas ut, ingen beräkning behövs

Om antal tävlande `M` modulo antal klassrum `N` **inte** ger en rest kan tävlande fördelas jämnt. Då ska på `N` rader printas `M / N` antal `*`

Om antal tävlande `M` modulo antal klassrum `N` ger en rest betyder det att tävlande inte kunde fördelas jämnt

Eftersom de tävlande inte kunde fördelas jämnt måste några rum fyllas på med resten `rest` tävlande som inte fick plats. Jag tar då `M - rest` för att få de antal tävlande som kunde fördelas jämnt, delar detta antal med `N` och får antalet tävlande `k` vilket just nu är minsta antalet tävlande det behöver vara i varje klassrum `i`.

Vid detta laget är det säkert att `rest` är mindre än `N`, de *kan* inte vara lika eftersom om de vore lika skulle det inte finnas någon rest, `rest` skulle ha blivit jämnt fördelad på varje klassrum `N` i modulo-operationen. Och `rest` kan inte heller vara större än `N` för då skulle numren upp till och med talet `N` redan i modulo-operationen varit med och fördelats lika mellan klassrummen `N` och resten skulle bli ett annat tal.

Fyller nu på ett klassrum i taget med 1 tills att `rest` är 0

**Min lösning i kod:**

```java
import java.util.Scanner;

class upprodun {
    public static void main(String[] args) {
        
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int M = sc.nextInt();
        
        // Not among the test cases //
        if (N <= 0) {
            }
        if (M <= 0) {
            while (N > 0) {
                System.out.println("");
                N--;
            }
        } // Not among the test cases //
        
        int rest = M % N;
        if (rest == 0) {
            int k = M / N;
            for (int i=0; i<N; i++) {
                for (int j=0; j<k; j++) {
                    System.out.print("*");
                }
                System.out.println();
            }
        } else {
            int minAmountOfContestantsInARoom = (M - rest) / N;
            // Each room will now have 1 contestant added until rest=0
            int k = 0;
            for (int i=0; i<N; i++) {
                if (rest > 0) {
                    k = minAmountOfContestantsInARoom + 1;
                    rest--;
                } else {
                    k = minAmountOfContestantsInARoom;
                }
                for (int j=0; j<k; j++) {
                    System.out.print("*");
                }
                System.out.println();
            }
        }
    }
}
```
