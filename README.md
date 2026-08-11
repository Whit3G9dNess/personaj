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

| Fișier          | Ce conține                                        |
|-----------------|---------------------------------------------------|
| `index.html`    | Scena întreagă: personaje, masă, animații         |
| `personaje.json`| Configurarea celor trei personaje, într-un loc    |
| `teste.html`    | Testele scenei, deschise tot în browser           |
| `SPEC.md`       | Descrierea personajelor, a scenei și etapele      |
| `PLAN.md`       | Planul de implementare pe faze și stadiul lui     |

## Teste

Testele se deschid în browser, ca și scena, dar au nevoie de un server local
fiindcă citesc `index.html`:

```
python -m http.server 8767 --directory personaj
```

apoi `http://localhost:8767/teste.html`. Pagina scrie sus câte au trecut și
câte au căzut.

## Stadiu

În lucru. Masa, lumina și ciclul complet al unei mâini funcționează — se împart
cărțile, fiecare joacă, unul câștigă și strânge cărțile, apoi se reia.

Clovnul e desenat: costum cârpit, guler cu volane, pălărie strâmbă, pantofi
mari. Stă cu spatele la privitor și tot întoarce capul spre vecini; când pierde
mâna, își aruncă în aer cărțile rămase.

Draconianul la fel: cap de dinozaur cu dinți și ochi galbeni, aripi pliate pe
spate, gheare. Stă aproape nemișcat până pierde o mână — atunci scoate foc pe
bot. Reptilianul e în costum, cu cravată și coadă; clipește rar, cu pleoapa
laterală, și nu reacționează la nimic — nici când câștigă.

Fiecare se gândește cât îi e felul înainte să pună cartea, lumina pâlpâie ușor,
cărțile au grosime, iar când cineva ia mâna ceilalți doi află: clovnul se
răsucește să-i vadă mutra, draconianul mârâie un fum pe nări, reptilianul nu
face nimic.

Localul e gata și populat: cei trei stau pe scaune la o masă cu picior și blat
gros, în spate e tejgheaua barului cu raftul de sticle și un barman care șterge
un pahar, iar în stânga, mai departe, la a doua masă, un om cu o mână de argint
joacă șah cu unul făcut tot din aur. La a treia masă, cea din față, vin din când
în când doi inși — de fiecare dată alții —, stau de vorbă și pleacă. Printre mese
umblă un chelner cu tava, care trece și pe la noi din când în când.

**Poți comanda.** Dă clic pe oricare dintre cei trei: chelnerul lasă tura și vine
la masă cu meniul — o listă scrisă, cu fel, cantitate și preț. Mici cu piure,
ciorbă de burtă, cartofi prăjiți, papanași, înghețată, Prigat, bere, vin — sau
nimic, dacă n-ai chef. Berea se toarnă la bar și se aude cum se umple paharul; micii se fac la
aragazul din capătul barului, cu fum cu tot. După ce comanda ajunge pe masă, se
bea și se mănâncă din ea din când în când. Sunetul pornește la primul clic,
fiindcă browserele nu-l lasă altfel.

**Partida o joci tu.** Cărțile au valori de la 1 la 10 și cea mai mare ia mâna.
Mâna clovnului o vezi jos, în fața mesei — când îți vine rândul, cărțile se
ridică puțin și se aprinde o lumină caldă sub ele. Dai clic pe una și o pune pe
masă. Dacă nu te hotărăști în douăsprezece secunde, joacă el — scena rămâne bună
și dacă doar te uiți la ea.

Draconianul și reptilianul joacă singuri. Dacă ai **Ollama** pornit pe
calculator, sunt conduși de modelul de acolo, fiecare cu firea lui; altfel, de o
judecată scrisă în cod — draconianul trântește cea mai mare carte, reptilianul
câștigă cu cât mai puțin. Nu trebuie să instalezi nimic: fără Ollama, scena
merge exact la fel de repede.
