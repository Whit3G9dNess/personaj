# Personaj

Trei personaje stau la o masă, într-o lumină caldă, și joacă cărți: un clovn de
circ, un draconian care scoate foc pe bot și un reptilian umanoid-crocodil.

O scenă animată care merge singură, la nesfârșit. Nu ai ce să apeși — te uiți
la ei cum joacă.

## Cum se deschide

Deschide `index.html` în browser. Atât.

Fără instalare, fără `npm install`, fără server. În Visual Studio Code poți folosi
și extensia *Live Server* dacă vrei reîncărcare automată la salvare.

## Cum e construit

Un singur fișier, `index.html`, care conține tot: HTML, CSS și JavaScript.
Personajele sunt desenate din forme geometrice direct în canvas 2D — nu există
nicio imagine în proiect. Codul și comentariile sunt în română.

## Fișiere

| Fișier      | Ce conține                                   |
|-------------|----------------------------------------------|
| `index.html`| Scena întreagă: personaje, masă, animații    |
| `SPEC.md`   | Descrierea personajelor, a scenei și etapele |
| `PLAN.md`   | Planul de implementare pe faze și stadiul lui|

## Stadiu

În lucru. Masa, lumina și ciclul complet al unei mâini funcționează — se împart
cărțile, fiecare joacă, unul câștigă și strânge cărțile, apoi se reia.

Personajele sunt deocamdată siluete colorate cu numele scris deasupra. Urmează
să fie desenate pe rând: clovnul, draconianul cu focul lui, apoi reptilianul.
Vezi [PLAN.md](PLAN.md).
