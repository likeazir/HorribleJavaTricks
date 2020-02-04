## Horrible Java Tricks

Intro: Diese Code Beispiele sind frei von mir erfunden. Wenn ihr einen dieser Fehler gemacht habt, nehmt es mit Humor und lernt draus. Ich selber bin zum Beispiel vor nicht allzu langer Zeit auf 3) hereingefallen.

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
    if (!cn.type() == ConnectionType.WORMHOLE
    	fl.recordDeparture(cur);
    	fl.recordArrival(dest);
}

```

Natürlich wird `fl.recordArrival()` immer aufgerufen. Lässt sich auf beliebige Kommentare übertragen. Besonders tödlich im Zusammenhang mit dem Schließen von Netzwerkverbindungen oder Multithreading.
