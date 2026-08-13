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

`personaje.json` e o fișă de citit, nu o piesă a scenei: are toate cele
douăsprezece personaje cu nume — firea cu care le răspunde modelul, replicile de
rezervă, locul în local și dimensiunile —, ca să se vadă toată distribuția pe o
pagină. Scena nu-l citește la pornire, fiindcă `index.html` se deschide direct
de pe `file://`, unde `fetch` cade; adevărul rămâne în cod, iar fișierul îl
oglindește.

## Fișiere

| Fișier          | Ce conține                                        |
|-----------------|---------------------------------------------------|
| `index.html`    | Scena întreagă: personaje, masă, animații         |
| `personaje.json`| Fișa tuturor personajelor din local, într-un loc   |
| `teste.html`    | Testele scenei, deschise tot în browser           |
| `SPEC.md`       | Descrierea personajelor, a scenei și etapele      |
| `PLAN.md`       | Planul de implementare pe faze și stadiul lui     |

## Teste

Testele se deschid în browser, ca și scena, dar au nevoie de un server local
fiindcă citesc `index.html`. Serverul e unul singur, pentru tot folderul, și stă
o treaptă mai sus:

```
python ../server.py
```

apoi `http://localhost:8765/personaj/teste.html`. Pagina scrie sus câte au trecut
și câte au căzut. Portul e mereu același, iar fișierele pleacă fără voie de
păstrare — un refresh aduce întotdeauna ce tocmai ai salvat.

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

Localul e gata și populat: cei trei stau pe scaune la o masă pătrată cu blat gros
și picior în X — clovnul în față, draconianul în spate, reptilianul în dreapta,
iar pe latura din stânga un dealer în vestă și papion, care împarte cărțile fără
să joace. În spate e tejgheaua barului cu raftul de sticle și un barman care șterge
un pahar, iar în stânga, mai departe, la a doua masă, un om cu o mână de argint
joacă șah cu unul făcut tot din aur. La a treia masă, cea din față, vin din când
în când doi inși — de fiecare dată alții —, stau de vorbă la o sticlă de vin
pusă în mijlocul mesei lor, și pleacă. Printre mese
umblă un chelner cu tava, care trece și pe la noi din când în când.

**Poți comanda.** Dă clic pe oricare dintre cei trei: chelnerul lasă tura și vine
la masă cu meniul — o listă scrisă, cu fel, cantitate și preț. Mici cu piure,
ciorbă de burtă, cartofi prăjiți, papanași, înghețată, Prigat, bere, vin — sau
nimic, dacă n-ai chef. Alegi câte vrei odată: apeși pe fiecare fel și se bifează,
apeși încă o dată și se șterge, iar jos scrie cât face tot. Când ai terminat,
zici **„doar atât"** și pleacă cu comanda, sau **„mă mai gândesc"** — atunci
duce ce-ai ales până acum și îți lasă meniul în față, pentru încă un rând.
Berea o toarnă barmanul, la mijlocul tejghelei, și se aude cum se umple paharul;
mâncarea o face bucătarul, în dreapta, la aragaz și la masa de amestecat, cu fum
cu tot — și durează, cum durează la orice bucătărie. Ce se golește nu rămâne pe
masă: trece chelnerul, strânge vasele și le duce la chiuveta din stânga barului,
unde le spală spălătorul de vase. Vinul vine cu sticla lui, pusă pe
mijlocul mesei — în golul de lângă clovn, ca să se vadă toată —, și cu paharul
gol; apeși pe sticlă și îți torni singur. După ce
comanda ajunge pe masă, se bea și se mănâncă din ea din când în când. Sunetul
pornește la primul clic, fiindcă browserele nu-l lasă altfel.

**Poți vorbi cu ei.** În colțul din dreapta-jos e un balon de vorbă: îl apeși,
alegi din listă cu cine vorbești — sau dai clic pe el direct în local — și
scrii. Îți răspund toți: draconianul mârâie scurt, barmanul te întreabă ce-ți
toarnă, spălătorul de vase se plânge de teancul din chiuvetă. Ce-ți răspund
apare și deasupra capului lor, în scenă. Dacă ai Ollama pornit, vorbele le scrie
modelul; dacă nu, fiecare are câteva ale lui — și partida merge înainte
oricum.

**Și poți vorbi despre altul.** Scrii un `@` (sau un `/`) și numele — se deschide
o listă cu cine e în local, alegi cu mouse-ul sau cu Enter. *„Barmanule, ce crezi
de @draconian?"* — și îți spune părerea lui despre draconian, nu o vorbă
oarecare. Numele merge scris scurt și fără diacritice: `@drac`, `@aur`,
`@spalator`. Pe clovn îl poate bârfi oricine, deși cu el nu poți vorbi: el ești
tu.

Cel din fața ta nu e în listă — n-are rost să-i spui pe nume când e chiar acolo.
În locul lui e **`@tine`**: *„ce crezi despre @tine?"*, și îți spune ce crede
despre el însuși.

**Te țin minte.** Dacă scrii „numele meu este Alex", localul reține și de-atunci
ți se adresează pe nume — toți, că e mic. Fiecare își ține și firul lui de
vorbă: te muți la altul și te întorci, vă găsiți unde ați rămas. Ține până la
refresh, nu mai departe.

Ce zice modelul trece printr-o sită înainte să se audă: își scoate numele scris
în față, ghilimelele, asteriscurile și mișcările dintre paranteze; vorba ruptă la
jumătate se taie la ultima propoziție încheiată, iar ce iese gol, în engleză sau
în alt alfabet se aruncă și răspunde din vorbele lui. Un model mic tot mai
greșește un acord din când în când — asta se rezolvă cu un model mai bun, nu de
aici.

**Partida o joci tu.** Cărțile au valori de la 1 la 10 și cea mai mare ia levata.
O mână ține trei levate: primești trei cărți și le joci pe toate, una pe levată,
abia apoi se împart altele — așa că întrebarea e mereu aceeași, *dau zecele
acum, sau îl țin pentru mai încolo?* Cărțile se pun cu fața în jos și se întorc
toate odată, deci nu vezi ce a jucat celălalt decât la sfârșit.

Din trei în trei mâini, cam așa, cuiva îi pică un **joker**: n-are cifră, are
căciula bufonului, și bate zecele — dar pierde în fața oricărei alte cărți, și
ia levata doar dacă chiar a căzut un zece pe masă. E o cacealma, nu o carte
mare.

Mâna clovnului o vezi jos, în fața mesei — când îți vine rândul, cărțile se
ridică puțin și se aprinde o lumină caldă sub ele. Dai clic pe una și o pune pe
masă. Dacă nu te hotărăști în douăsprezece secunde, joacă el — scena rămâne bună
și dacă doar te uiți la ea.

Dealerul împarte și, cam la una din șapte mâini, zice „hai că joc și eu o rundă"
și intră și el în joc — dar niciodată de două ori la rând.

Draconianul și reptilianul joacă singuri. Dacă ai **Ollama** pornit pe
calculator, sunt conduși de modelul de acolo, fiecare cu firea lui; altfel, de o
judecată scrisă în cod — draconianul trântește cea mai mare carte, reptilianul
își drămuiește mâna și ține tăria pentru ultima levată. Niciunul nu trage cu
ochiul la cărțile întoarse de pe masă: joacă din ce are în mână, ca și tine. Nu
trebuie să instalezi nimic: fără Ollama, scena merge exact la fel de repede.
