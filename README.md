## Horrible Java Tricks

#### Intro
Diese Code Beispiele sind frei erfunden. Wenn ihr einen dieser Fehler gemacht habt, nehmt es mit Humor und lernt draus. Ich selber bin zum Beispiel vor nicht allzu langer Zeit auf 3) hereingefallen.

#### 1) Ist das C oder Java?

```Java
int [][] foo;
int foo [][];
int [] foo [];
```

Die Deklarationen sind äquivalent.

#### 2) We need to go deeper

Gute Programmierer schaffen es, bis zu 10 Tabs einzurücken, ohne den Überblick zu verlieren. 
Am besten schachtelt man so viele if, else, {}, und () wie möglich.

Extra: nested ternary operators, z.B.:

```java
boolean b = true;
int x = b ? (x = b ? 1 : 2) : 3;
```

Versucht mal zu erraten was das tut.

#### 3) The Trickster

```Java
if (true)
    if (false)
        foo();
else
    bar();
```

Natürlich gehört das else zum zweiten if, sieht man doch.

#### 4) What am I?

```Java
class Foo{

static Foo Foo(){
    return new Foo();
}
}
```

Das keyword new ist einfach nur lästig, wer braucht schon einen Konstruktor.

#### 5) The Classic

```java
if (b == true)
```

Was soll ich dazu sagen?

#### 7) Der miseleading Kommentar

```Java
public void finish(Flightrecorder fl, BeaconConnection cn, Beacon cur, Beacon dest){
    /*if we came through a wormhole arrival and departure already have been 
    recorded, so we must not do that again*/
    if (!(cn.type() == ConnectionType.WORMHOLE))
    	fl.recordDeparture(cur);
    	fl.recordArrival(dest);
}

```

Natürlich wird `fl.recordArrival(dest)` immer aufgerufen. Lässt sich auf beliebige Kommentare übertragen. Besonders tödlich im Zusammenhang mit dem Schließen von Netzwerkverbindungen oder Multithreading.
Eine besonders großes Problem für leichtgläubige Tutoren. Wo ist eigentlich Punkt 6?

#### 8) Improvise. Adapt. Overcome.

Da es bekanntlich keine "echten" globale Variablen in Java gibt, kann man static abusen. Am besten alles static setzen, so braucht man keine Instanzen der Objekte erstellen. Diese komische Objektorientierung sorgt sowieso nur für Verwirrung.

Sneaky Bonus: `.interrupt()` überschreiben und `static boolean isInterrupted` als Flag einführen.

#### 9) [You spin me right round baby right round](https://www.youtube.com/watch?v=PGNiXGX2nLU&feature=youtu.be&t=61)

```Java
// Wait until the resource is free
while(!isResourceFree()) {
}
```

Diese famose Konstruktion wird Spin-Lock genannt und ist eine Form von busy-waiting.
Genauso wie bei dem verlinkten Song handelt es sich hierbei fast immer um eine Verschwendung von kostbaren Ressourcen und CPU-Zeit.

Das lässt sich dann wie folgt mit Trick 7) kombinieren:
```Java
// Wait until the resource is free
boolean stop = isResourceFree();
// if-Block using stop as condition
while(!stop) {
}
```
*Ahhhh! Hilfe! Mach es weg!*
Das hier läuft in Wirklichkeit entweder durch, oder landet in einer Endlosschleife.

#### 10) Try this one simple trick to fix your Deadlocks

```Java
class MagicThread extends Thread {
    @Override
    public void start() {
        //write your code here
    }
}
```
Im obigen Beispiel für einen `MagicThread` wurde die Methode `.start()` aus `Thread` überschrieben. Wenn `Thread` nur wie im gezeigten `MagicThread` verwendet wird, kann trivialerweise kein Deadlock entstehen* Der Beweis ist dem Leser überlassen. Als Lektüre hierfür bietet sich der [Sourcecode einer beliebigen JVM an](https://hg.openjdk.java.net/jdk/jdk13/file/0368f3a073a9/src/java.base/share/classes/java/lang/Thread.java#l781).

<sup><sup>*(Ein Nachteil ist lediglich die sequentiellen Ausführung.)</sup></sup>

#### 11) Uncheckify all your Exceptions

Wer kennt es nicht, man möchte ein Problem mit einem Stream lösen, und plötzlich muss man sich um checked Exceptions kümmern. 
Blöderweise kann man bei Streams auch nicht deklarieren, dass sie weitergereicht werden, sondern muss einen lästigen try-catch-Block schreiben. Dilletanten würden an dieser Stelle einfach den Stacktrace printen, oder eine unchecked Exception weiterwerfen. 
Nicht so Mr. Felix Hohenadel, der vor dem Problem stand, dass die erzeugten checked Exceptions - laut Aufgabenstellung - an den Aufrufer weitergeworfen werden *müssen*.

Dafür ist folgender Exception Handler vorgesehen. 
```Java
private static <E extends Throwable> void ExceptionHandler(Throwable t) throws E {
    throw (E) t;
}
```
Dieser wirft die übergebene Exception noch einmal - allerdings mit dem generischen Typen E, der von Exception erbt. Damit wäre der Compiler überlistet. Was ist aber mit der JVM? Dank Type Erasure funktioniert es auch zur Runtime.
Angewandt sieht das ganze dann so aus:

```Java
public void foo() {
    bar.stream().map(
        x -> {
            try {
                throw new Exception();
            } catch (Exception e) {
                ExceptionHandler(e);
            }
            return x;
        })....
```

