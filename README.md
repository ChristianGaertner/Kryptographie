# Kryptographie

Los gehts! Wer nicht weiß was oder wer das ist sollte jetzt aufpassen!

Kryptographie ist die Wissenschaft der Verschlüsselung und befasst sich mit der Geheimhaltung von vertraulichen Informationen.


## Doch wie funktioniert das ganze?

Es gibt viele verschiedene Arten der Verschlüsselung. Hier werden wir uns auf 3 mehr oder weniger simple Verfahren konzentrieren.


### Caesar

Caesar ist zugegeben die einfachste aller Verschlüsselungen und viele bereits aus dem Kindes-Alter bekannt, damals noch mit Dreh-Kärtchen oder ähnlichem.

Basieren tut diese Verschlüsselungsart auf der Verschiebung von einzelne Buchstaben.

Ein Beispiel:

`ABCD => BCDE`

Angewendet wurde der Schlüssel `A => B`, oder einfach nur `B`.

Wie man unschwer erkennen kann, lässt sich das ganze schnell und einfach durchführen (und ebenso schnell zurückrechnen).

Al­go­rith­mische Darstellung (Pseudo-Code):

(Buchstaben werden als Zahl dargestellt (A=1, B=2, ..., Z=25).

Buchstabe `x` soll verschlüsselt werden mit Schlüssel `s`:

```javascript
V(s,x) = ((int) x +  (int) s) mod 26
```

Und entschlüsselt wird dann sehr ähnlich:

```javascript
E(s,x) = ((int)x - (int) s) mod 26
```

**Achtung:** Diese Methode funktioniert _nur_ mit Buchstaben im Alphabet _(A-Z)_ und auch nur groß _oder_ klein Buchstaben. Es wird also empfohlen vorher alle Buchstaben in eine Schreibweise zu konvertieren und Leerzeichen zu entfernen.

```javascript
V(s.toUpperCase(), x.toUpperCase.replace(/\s+/g, '')) = ((int) x +  (int) s) mod 26
E(s.toUpperCase(), x.toUpperCase.replace(/\s+/g, '')) = ((int)x - (int) s) mod 26
```
In diesem Beispiel wurden alle Zeichen erst in Großbuchstaben konvertiert (`.toUpperCase()`) und anschließend wird mit Hilfe von regulären Ausdrücken (_RegEx_) Leerzeichen entfernt (`.replace(/\s+/g, '')`). Natürlich gibt uns diese Methode noch immer Fehler, wenn Sonderzeichen im Text vorhanden sind.

Eine mögliche Lösung wäre nicht streng der Spezifikation der Caesar Verschlüsselung zu folgen und auf den _ASCII Code_ (_American Standard Code for Information Interchange_) zu setzten und das Alphabet zu verlassen. Anstatt bei Z wieder zu A umzuschlagen wird einfach weiter im ASCII Code gegangen - und somit jedes Zeichen verschlüsseln zu können. Dadurch werden unsere Funktionen auch wieder um ein vielfaches Einfacher:

```javascript
V(s,x) = (x + s)
E(s,x) = (x - s)
// s & x müssen als Zahl vorliegen (ASCII Code)
```


### Vigenère

Um die Probleme der schnellen und einfach Entschlüsselung des Caesar Chiffres vorzubeugen wurde einige Zeit später die Vigenère Verschlüsselung entwickelt. Jene basiert zwar immer noch auf dem gleichen Prinzip wie Caesar, nämlich dem Verschieben von Buchstaben, aber ist etwas sicherer, da der Schlüssel nicht nur aus einem Buchstaben besteht.

Konkret sieht das ganze so aus, dass ein Schlüssel **Wort** genutzt wird. Auf den ersten Buchstabe des Klartextes wird der erste Buchstabe des Schlüssel-Wortes angewendet, immer 1. & 1. und 2. & 2.

Bis das Ende des Schlüssel-Wortes erreicht wird. Nun wird wieder mit dem 1. Buchstabe des Schlüssel Wortes angefangen, usw.

Pseudo-Code:

```javascript
V(s,x) = (x + s) // Caesar!

for (index i in klartext) {
  put V(schluessel[i mod schluessel.length], klartext[i])
}
```

Ein Beispiel:

```javascript
klartext = "GEHEIMENACHRICHT"
schluessel = "KEY"
// Vigenère ausführen
// stdout:
RJGPNLPSZNMQTHGE
```


### RSA

Der Aufmerksame Leser wird schnell festgestellt haben, dass beide Methoden eine große Schwäche aufweisen - der Transfer des Schlüssels. Eine kleine Darstellung:

```

|------|                |-----------|              |---------|
|      |   Cipher       |Böse Hacker|   Cipher     |         |
| Bob  | ===========>   | Cipher+Key| ===========> |  Alice  |
|      |     Key        | =Klartext |      Key     |         |
|------|                |-----------|              |---------|
```

Wie man sieht kann man den Key nicht mit übertragen, da sonst jeder den Cipher entschlüsseln könnte.

Einmal einen Schlüssel wählen in einem private Gespräch ist meist ebenfalls keine Lösung, da oft nur mit einem Server kommuniziert wird, den man nicht mal eben zu StarBucks einladen kann. Die Lösung lautet asymmetrische Verschlüsselung.

Bisher haben wir uns symmetrische Verschlüsselungs-Methoden angeguckt, ein Schlüssel wird für Ver- und Entschlüsselung genutzt. Eine asymmetrische Herangehensweise, wie _RSA_ sie ist, arbeitet etwas anders.

Hierzu wieder ein kleines Beispiel.

Der Klartext wird in eine Box gelegt und mit einem Schloss verschlossen. Der Empfänger braucht jetzt den **gleichen** Schlüssel um die Nachricht zu lesen. So hat Caesar funktioniert.

RSA geht einen anderen Weg.

Zunächst muss ich wissen, _wem_ ich eine Nachricht schicken will. Von dieser Person fordere ich nun ein Schloss an, mit diesem verschließe ich die Box und schicke ihn an den Empfänger. Nur dieser hat den Schlüssel und kann somit als einziger die Box öffnen.

Das Schloss fungiert als **öffentlicher Schlüssel** und der Schlüssel, der niemanden gegeben wird, fungiert als **privater Schlüssel** - _asymmetrische_.


Doch was sind das für Schlüssel auch wieder Buchstaben zum Verschieben - Nein. RSA ist _etwas_ komplexer.

Erzeugen des Schlüssel-Paares:

* Wählen von zwei Primzahlen `p=11` und `q=13` (in der Realtität sollten dies große Zahlen sein!)
* Ein Teil des öffentlichen Schlüssel `N = p * q = 143`
* Nun wird der zweite Teil des Schlüssels generiert:

Erreicht wird dies mit der eulersche  φ-Funktion:
```
φ(N) = (p - 1)(q - 1)= φ(143) = (11 - 1)(13 - 1) = 120
```

Der zweite Teil des Schlüssel wird `e` genannt und muss zu `120` teilerfremd sein. Also z.B. `e=23`

* Der öffentliche Schlüssel ist somit `N=143;e=23`
* Der geheime Schlüssel berechnet sich aus den Inversen zu `e`.

```
e * d + k * φ(N) = 1 = ggT(e, φ(N))
// Konkret:
23 * d + k * 120 = 1 = ggT(23, 120)
```

* Nun lässt sich daraus der geheime Schlüssel berechnen: `d=47`

### Das Verschlüsseln

Die Klartext Nachricht wird in den folgenden Darstellung stets `m` genannt und die verschlüsselte Nachricht `c`.

```
c ≡ m^e mod N
// m < N !!
```

Beispiel (wir verschlüsseln die Zahl 4):

```
75 ≡ 4^23 mod 143
```

Diesen Cipher können wir nun getrost verschicken. Auf der anderen Seite kann `c=75` wieder entschlüsselt werden, gegeben man besitzt den geheimen Schlüssel:

```
m ≡ c^d mod N
```

Konkret:

```
4 ≡ 75^47 mod N
```


Und so hat der private Schlüssel `d` nie das System des Empfängers verlassen - nur jener kann die Nachricht lesen. Doch wie sicher ist das System, wenn jeder den öffentlichen Schlüssel `N` kennt, aus dem man meinen privaten Schlüssel errechnet hat.

In der Tat lässt sich das ganze knacken, aber es ist sehr schwer. Man muss wissen, aus welchen Primzahlen `N` errechnet worden ist. Dies ist eine komplexe Rechenoperation, wenn wir mit sehr großen Zahlen zu tun haben.

Computer schaffen es heutzutage problemlos 512 Byte Schlüssel zu knacken.

Vorgehensweise:

Alle Primzahlen kenn bis `sqrt(N)` und folgenden Pseudo-Code ausführen:

```javascript

for (p in primes.until(sqrt(N)) {
   q = N / p
   if (primes.contains(q)) {
      return {p, q} // Geknackt
   }
}
```

Problem hierbei ist natürlich, dass die Anzahl der Rechenoperation stark steigt, je größer N ist.

Momentan können 4096 Byte Schlüssel noch nicht geknackt werden, aber vorraus gesagt wurde, dass es in etwa 10 Jahren durchaus möglich ist. Unsere Daten sind also auf Zeit sicher.

