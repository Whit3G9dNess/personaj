# Plan de implementare — Personaj

Planul de lucru pentru scena descrisă în [SPEC.md](SPEC.md).

Etapele din SPEC §6 sunt ordonate *orizontal*: întâi toate personajele statice,
apoi toate animațiile, apoi ciclul de joc. Cu ordinea aceea, scena „se mișcă"
abia la penultima etapă. Planul de mai jos le reordonează *vertical*: personajele
sunt la început niște siluete simple, dar ciclul complet al unei mâini merge
devreme. De la felia verticală încolo, fiecare fază doar înlocuiește ceva ce
funcționează deja.

## Stadiu

| Fază | Ce conține | Stare |
|------|------------|-------|
| 0 | Scheletul: canvas, buclă, utilitare | ✅ gata |
| 1 | Masa, lumina, cele trei locuri | ✅ gata |
| 2 | Cărțile și ciclul complet — **felia verticală** | ✅ gata |
| 3 | Clovnul | ✅ gata |
| 4 | Draconianul + focul | ✅ gata |
| 5 | Reptilianul | ✅ gata |
| 6 | Rafinare | ✅ gata |
| 7 | Localul: masa cu scaune, barul, masa a doua | ✅ gata |
| 8 | Lumea din local: clienții, chelnerul, barmanul | ✅ gata |
| 9 | Comanda și sunetul | ⬜ |

---

## Faza 0 — Scheletul

- `index.html`, fișier unic, secțiuni numerotate, comentarii în română.
- Canvas pe tot ecranul, redimensionare cu `devicePixelRatio`.
- Buclă `requestAnimationFrame` cu `dt` (timp delta) — nimic nu depinde de FPS.
- Utilitare de desen: interpolare, ușurare (easing), capsulă, elipsă.

**Verificare:** pagina se deschide, canvas-ul se redimensionează cu fereastra.

## Faza 1 — Masa, lumina, cele trei locuri

- Masă rotundă văzută ușor de sus (elipsă turtită pe verticală).
- Lumină caldă de deasupra: gradient radial pe masă, con de lumină peste scenă,
  vignetă spre negru. Pereții nu se desenează.
- Trei locuri calculate la 120°, fiecare cu sistemul lui de coordonate — un
  personaj se desenează mereu „în față", indiferent la ce loc stă.
- În fiecare loc, o siluetă-placeholder colorată diferit.

**Verificare:** scena arată ca o masă luminată cu trei forme așezate la ea.

## Faza 2 — Cărțile și ciclul complet — felia verticală

- O singură funcție de desen pentru carte, folosită peste tot (teanc, mână, masă).
- Cărți ca obiecte animate: fiecare are poziția curentă și o țintă spre care
  interpolează. Nu există „sărituri" de poziție.
- Mașina de stări a unei mâini:
  `împărțire → gândire → jucare → câștigător → reacție → pauză → (reia)`.
- Împărțire câte o carte pe rând, gândire de durată diferită pentru fiecare,
  cărți puse pe masă în ordine, câștigătorul trage cărțile spre el.
- Reacții deocamdată abstracte: siluetele tresaltă sau se lasă în jos.
- Câștigătorul unei mâini începe împărțirea următoare.

**Verificare:** deschizi pagina și te uiți la trei mâini la rând fără să se
blocheze sau să se desincronizeze nimic.

## Faza 3 — Clovnul

Primul personaj real. Definește contractul respectat apoi de ceilalți doi:

```js
{ nume, unghi, culori, deseneaza(ctx, t), reactie(tip) }
```

- Costum cu petice roșu/galben/turcoaz, nas rotund, pantofi mari.
- Idle: legănat pe scaun, respirație, trage cu ochiul la vecini.
- Reacții: își ascunde cărțile prea teatral; când pierde, aruncă cărțile în aer.

Clovnul stă cu spatele la privitor, așa că i se vede costumul din spate și
ceafa. Fața se ghicește doar când întoarce capul spre vecini — atunci nasul
iese de după obraz. `deseneazaPersonaj` a fost despărțit în două: mișcarea
comună (respirație, legănat, tresărire) rămâne a scenei, iar desenul propriu-zis
îl face personajul, dacă are `deseneaza`; altfel se desenează silueta.

**Verificare:** clovnul e desenat, ceilalți doi rămân siluete, scena merge la fel.

## Faza 4 — Draconianul

- Masiv, ocupă cel mai mult loc. Cap de dinozaur, două aripi pliate pe spate,
  gheare care țin cărți prea mici pentru ele.
- Idle: aproape imobil, respirație grea, aripile tresar.
- Foc pe bot — sistem simplu de particule, refolosibil. Se declanșează când pierde.

Sistemul de particule nu știe nimic despre draconian: îi spui unde să aprindă și
încotro (`aprindeFoc(x, y, unghi, câte, putere)`). Îl va refolosi Faza 7 pentru
lumânările de pe mese și aburul din spatele barului.

Draconianul a adăugat două lucruri la contractul comun, amândouă opționale:
`p.gura`, un punct de pe corp dat în unități U, mutat odată cu scena, și
`p.actualizeaza(dt)`, pentru personajele care au ceva de întreținut în timp
(la el, jetul de foc care ține o clipă după reacție).

## Faza 5 — Reptilianul

- Bot lung, solzi verzi-măslinii, postură dreaptă, îmbrăcat.
- Idle minimal: aproape nemișcat, clipit rar cu pleoapa laterală.
- Când câștigă nu arată nimic — doar trage cărțile. Contrastul e tot efectul.

„Nu arată nimic" e implementat ca refuz, nu ca uitare: `reactie` îi anulează și
tresărirea comună, pe care o primesc toți de la mașina de stări. Două teste țin
asta pe loc — desenul lui trebuie să iasă identic după câștig și după pierdere,
în timp ce al clovnului trebuie să se schimbe.

Odată desenați toți trei, silueta-placeholder a dispărut din cod, împreună cu
numele scrise deasupra și cu culorile care o hrăneau. Fiecare personaj își ține
acum paleta lui (`CLOVN`, `DRACON`, `REPT`).

## Faza 6 — Rafinare

- Timp de gândire caracteristic fiecăruia (clovnul rapid și haotic, draconianul
  lent, reptilianul constant).
- Umbre proiectate pe masă, cărți cu grosime, tremur ușor de lumină.
- Reacții încrucișate: clovnul trage cu ochiul, draconianul mârâie.

Timpul de gândire a devenit `timpDeGandire(p)`, din două numere: `gandire`, cât
îi trebuie de obicei, și `haos`, cât de mult sare de la o dată la alta. Clovnul
e cel mai rapid dar cel mai imprevizibil, reptilianul face de fiecare dată cam
la fel. Testele verifică și media, și împrăștierea — altfel „caracteristic" ar
fi rămas doar trei numere diferite.

Reacțiile încrucișate au adus un al cincilea eveniment, `vecinCastiga`, singurul
despre altcineva. Nu zguduie pe nimeni, doar spune cine a luat mâna: clovnul se
răsucește spre el, draconianul mârâie (un fum scurt pe nări, din același sistem
de particule ca focul), reptilianul nu face nimic.

Umbrele cad dinspre centrul mesei spre exterior, fiindcă lampa e deasupra
centrului. Deocamdată se văd prost: personajele stau în afara blatului, așa că
umbra lor cade pe podeaua neagră. Se vor vedea când vor sta pe scaune, la masă,
la Faza 7.

## Faza 7 — Localul

Până aici scena e o masă suspendată în întuneric. Faza asta îi dă un loc:
suntem într-un bar, iar masa celor trei e doar una dintre mese.

Toată faza e decor: nu se mișcă nimic în ea și nu atinge ciclul jocului.

**Ce se adaugă**

- **Masa și scaunele**, făcute ca lumea. Până acum masa e o elipsă cu un
  gradient, iar cei trei stau în picioare pe lângă ea. Acum: blat cu grosime și
  muchie, picior de masă, și trei scaune pe care personajele chiar stau —
  așezate, nu plutind. De aici încolo umbrele lor cad pe blat, unde se și văd
  (vezi Faza 6), iar brațele ajung la cărți fiindcă sunt la distanța potrivită.
- **Tejgheaua barului**, în spate, cu sticle de sucuri colorate pe raft și o
  lumină proprie, mai rece decât cea de deasupra mesei.
- **A doua masă**, deocamdată goală: în lateral, mai departe și mai mică —
  perspectiva se face din scară și din cât de întunecat e desenul, camera rămâne
  fixă.

E singura fază care umblă la ceva ce merge deja: `deseneazaMasa` și locurile de
la masă. Restul, până acum, doar s-a adăugat. De asta vine singură, înaintea a
tot ce e viu în local.

**Cum stau pe scaun.** Nu din desen, ci din ordinea desenării și din unde sunt
puse locurile. Cei din spate sunt trași înăuntru, până le intră poalele sub
blat, iar masa se desenează după ei și îi acoperă de la brâu în jos. Cel din
față stă dincolo de marginea dinspre noi, se desenează după masă, iar peste
picioarele lui trece spătarul scaunului. Pantofii rămân la vedere pe sub el.

Locurile nu mai stau pe un cerc. Privim masa de sus, sub un unghi mic, așa că
partea dinspre noi și cea din fund nu se văd la fel: cei din spate sunt la 0,86
din raza mesei pe orizontală și la 0,55 pe verticală, cel din față la 1,18.
Trei teste țin asta pe loc — unul chiar calculează unde e marginea blatului la
x-ul fiecăruia și cere ca baza lui să fie sub ea. Prima variantă trecea toate
testele și arăta tot a oameni în picioare; testul ăsta a lipsit exact atât.

**Verificare:** scena arată a bar gol, iar partida merge exact ca înainte — cei
trei stau acum pe scaune, cu umbrele pe blat.

## Faza 8 — Lumea din local

Localul capătă oameni. Amândoi de aici sunt de fundal: nu joacă, nu știu de
cărți, au ciclul lor.

- **Doi clienți** la masa a doua, care **joacă șah**: unul e om în carne și oase
  cu o mână de aur, celălalt e făcut tot din aur, din cap până-n picioare. Mută
  pe rând, la câteva secunde, fără reguli adevărate — ca și cărțile de la masa
  noastră.
- **Chelnerul**, care se plimbă prin local cu tava: pe la bar, pe la masa a
  doua, și pe la masa noastră din când în când. Nu așteaptă să fie chemat —
  face turul lui oricum. Se oprește puțin la fiecare popas, apoi merge mai
  departe. Costum clasic alb-negru, șorț lung, mustață neagră, păr brunet.
- **Barmanul**, care se plimbă în spatele tejghelei ștergând un pahar: cămașă
  albă cu mânecile suflecate, bretele, papion, barbă. Nu iese niciodată din
  spatele barului.

Contractul e cel de la masa mare (`deseneaza`, `actualizeaza`), ca să nu apară
un al doilea fel de a face un personaj. Diferența e doar că pe ăștia nu-i cheamă
nimeni: nu primesc evenimente de la mașina de stări a jocului, și un test
verifică asta — după o mână întreagă, niciunul nu știe cine a câștigat.

Toți patru sunt croiți pe același trup de bază, fiindcă sunt fundal: nu trebuie
să fie cineva anume. Fiecare desenează peste el ce are în plus — mâna de aur,
șorțul și tava, bretelele și paharul.

Adâncimea o dă tot ordinea desenării, ca la Faza 7: barmanul e prins între raft
și tejghea, cei doi șahiști între piciorul mesei și blat, iar chelnerul se
desenează înainte sau după masa noastră, după cum e mai departe sau mai aproape
decât ea.

Mișcarea dintr-un loc în altul nu mai e a cărților: `mutaObiect` /
`actualizeazaObiect` mută la fel și piesele de șah.

**Verificare:** localul e viu, dar dacă te uiți la masa celor trei, nimic din ce
se întâmplă în spate nu te trage cu ochiul.

**Rămas de aici:** reptilianul era prea mic față de ceilalți doi și a fost mărit
la 13×25U — acum e al doilea ca mărime, după draconian.

## Faza 9 — Comanda și sunetul

Aici scena capătă singurul ei buton, și primul sunet. Vine ultima fiindcă are
nevoie de tot ce e înainte: de chelner ca să aducă, de bar ca să toarne, de masă
ca să pună paharul pe ea.

Comanzi pentru oricare dintre cei trei, iar restul se întâmplă singur:

1. **Alegi** — întâi pentru cine, apoi ce: suc, bere, vin sau **nimic**. Meniul
   stă mic și discret lângă personajul ales, iar până nu-l atingi, scena merge
   exact ca până acum. Fiecare dintre cei trei poate avea paharul lui pe masă.

   Refuzul e o opțiune la fel de bună ca celelalte, nu o ieșire de avarie: dacă
   alegi „nimic, mulțumesc", chelnerul dă din cap și își vede de tura lui. La
   fel se întâmplă și dacă închizi meniul fără să alegi. Scena trebuie să fie
   la fel de bună neatinsă — asta e starea ei normală, la urma urmei.
2. **Chelnerul se duce la bar** și toarnă. Se **aude turnatul**, altfel pentru
   fiecare băutură: sucul scurt și subțire, berea cu spumă care sfârâie după,
   vinul mai gros și mai grav. Sunetul urcă în înălțime pe măsură ce paharul
   se umple — de asta se aude că e un pahar, nu un zgomot oarecare.
3. **Aduce paharul** la masa celor trei și îl pune lângă cel care a comandat, cu
   un „poc" scurt de sticlă pe lemn.
4. **Se bea din când în când**, fiecare cum îi e felul: clovnul ridică paharul
   teatral și soarbe zgomotos, draconianul îl dă pe gât dintr-o dată,
   reptilianul ia o înghițitură mică și-l pune la loc exact unde era. Nivelul
   scade de fiecare dată. Când s-a golit, paharul rămâne pe masă — poți comanda
   din nou.

Comanda nu oprește partida: cărțile se împart și se joacă mai departe cât timp
chelnerul e pe drum. Dacă mâna se termină între timp, cu atât mai bine — se
suprapun.

### Sunetul

Sunetele se fac în cod, cu WebAudio, exact cum se fac și personajele: din forme
simple, fără niciun fișier. Zgomot filtrat pentru turnat, un filtru care urcă
pentru paharul care se umple, click-uri scurte pentru bule și pentru pahar.

Un browser nu lasă sunetul să pornească nechemat, deci contextul audio se
deschide la primul clic — care e chiar clicul de comandă. Până atunci, scena e
mută, așa cum e și acum.

**Verificare:** comanzi o bere, o auzi turnată și, peste puțin timp, cel pentru
care ai comandat are un pahar din care soarbe. Partida n-a stat nicio clipă.

---

## Reguli pentru fazele 7–9

1. Fundalul nu are voie să fure atenția: mai mic, mai întunecat, mai puțin
   detaliat, fără culori la fel de tari ca la cei trei.
2. Fundalul nu știe de ciclul jocului și nici jocul de el. Două bucle separate,
   care nu se așteaptă una pe alta.
3. Personajele de fundal respectă același contract ca cei trei, ca să nu apară
   un al doilea fel de a face un personaj.
4. Chelnerul se mișcă pe un drum dat în unități U, ca tot restul, ca să se
   scaleze corect pe orice ecran.
5. Sunetul e un adaos, nu o condiție: cu boxele închise nu se pierde nimic din
   ce se vede.
6. Comanda e singura interacțiune din toată scena. Nu se adaugă a doua.

## Ce rămâne de hotărât la fazele 7–9

- Cum arată meniul de comandă: pahare desenate lângă personaj, sau un rând de
  cuvinte jos.
- Cum ceri meniul: clic pe personaj, sau apare singur când trece chelnerul pe
  la masa noastră.

---

## Decizii tehnice

1. **Siluetele respectă același contract ca personajele reale.** Înlocuirea lor
   la Fazele 3-5 e o modificare locală, nu o rescriere.
2. **Tot ce ține de timp trece prin `dt`**, nu prin numărători de cadre. Scena
   arată la fel pe un ecran de 60 Hz și pe unul de 144 Hz.
3. **Personajele nu cunosc regulile.** Mașina de stări le trimite doar
   evenimente: `gandeste`, `joaca`, `castig`, `pierdere`.
4. **Toate dimensiunile sunt în unități `U`** (a suta parte din latura mică a
   ferestrei). Scena se scalează corect pe orice ecran.

## Teste

De la Faza 3 încolo se lucrează cu testul scris întâi. Testele stau în
[teste.html](teste.html) și nu au nevoie de nimic instalat.

Ele nu copiază codul scenei: citesc `index.html`, îl rulează cu o pânză falsă
care ține minte ce s-a desenat și unde, apoi întreabă desenul — „există un cerc
mic, roșu, în dreptul capului?". Așa fișierul testat rămâne exact fișierul
livrat, iar regula „un singur fișier" nu se strică din cauza testelor.

Fiindcă testele citesc `index.html` cu `fetch`, au nevoie de un server local —
de pe `file://` nu merg:

```
python -m http.server 8767 --directory personaj
```

apoi `http://localhost:8767/teste.html`.

## Rămas de făcut

- A rămas o singură siluetă cu numele scris deasupra, reptilianul. Numele e un
  ajutor temporar de dezvoltare și dispare la Faza 5.
- Brațul dinspre privitor al clovnului e acoperit de cărțile din mână. Se vede
  când personajul le ridică; de rezolvat, dacă deranjează, la Faza 6.
