# Personaj — Partida de cărți

Spec minimal. Trei personaje stau la o masă și joacă cărți.

## 1. Ce este

O scenă animată, într-un singur fișier `index.html`, desenată în canvas 2D și
sonorizată în WebAudio. Fără biblioteci, fără build, fără fișiere externe — nici
imagini, nici sunete: se deschide direct în browser.

Cărțile au valori de la 1 la 10 și cea mai mare ia mâna. **Mâna clovnului o joci
tu**: o vezi jos, în fața mesei, și alegi ce pune pe masă. Ceilalți doi joacă
singuri. Dacă nu alegi nimic, joacă și el după douăsprezece secunde — scena
rămâne bună și dacă doar te uiți la ea.

## 2. Personajele

### 2.1 Clovnul
Costum colorat (petice de roșu, galben, turcoaz), nas rotund, pantofi mari.
Amuzant, jucăuș. Nu stă locului: se leagănă pe scaun, își ascunde cărțile prea
teatral, trage cu ochiul la vecini. Când pierde o mână, aruncă cărțile în aer.

### 2.2 Draconianul
Masiv, ocupă cel mai mult loc la masă. Cap de dinozaur, două aripi pliate pe
spate, două labe-mâini, două labe-picioare. Fioros, tăcut. Ține cărțile în gheare,
prea mici pentru el. Scoate foc pe bot când e nervos — și e nervos des.

### 2.3 Reptilianul
Umanoid-crocodil: bot lung, solzi verzi-măslinii, postură dreaptă, îmbrăcat.
Calm, calculat, cel mai bun jucător dintre cei trei. Aproape nu se mișcă;
clipește rar, cu pleoapa laterală. Când câștigă, nu arată nimic.

## 3. Scena

Cei trei joacă la o masă dintr-un bar.

- Masă rotundă cu blat gros și picior, văzută ușor de sus, într-o lumină caldă
  de deasupra. Cei trei stau pe scaune, la masă.
- Sunt așezați la 120° unul de altul; camera fixă.
- În centru: teancul de cărți și mâna jucată curent.
- În fundal, de-a latul scenei: barul — tejghea cu panou verde, două polițe
  luminate cu sticle, lămpi atârnate deasupra, plante, scaune înalte trase la
  tejghea și un barman care se plimbă în spatele ei.
- În stânga-spate, mai departe și mai mică: a doua masă, la care doi clienți
  joacă șah — un om cu o mână de argint și unul făcut tot din aur.
- În stânga-față: a treia masă, goală mai tot timpul. Din când în când vin doi
  inși, stau de vorbă și pleacă. Cum se așază, au o sticlă de vin în mijlocul
  mesei lor.
- Printre mese umblă un chelner cu tava, care trece și pe la masa noastră din
  când în când.
- Nu se desenează pereți — barul și masa a doua ies din întuneric doar cât să se
  înțeleagă unde suntem.
- Masa celor trei rămâne singura complet luminată. Tot ce e în jur e mai
  întunecat, mai mic și mai puțin detaliat, ca să nu fure atenția.

## 4. Ciclul unei mâini

1. Se împart cărțile (animație de distribuire, câte una pe rând).
2. Fiecare personaj „se gândește" — un timp diferit pentru fiecare.
3. Fiecare pune o carte pe masă, în ordine. Când e rândul clovnului, masa te
   așteaptă pe tine.
4. Cea mai mare carte ia mâna; câștigătorul trage cărțile spre el.
5. Reacții: clovnul exagerează, draconianul scoate foc, reptilianul tace.
6. Pauză scurtă, apoi se reia de la 1.

Bucla merge la nesfârșit. Dacă nu atingi nimic, joacă și el în locul tău.

## 4b. Comanda de la bar

Chemi chelnerul cu un clic pe personajul pentru care vrei să comanzi. Meniul e o
listă scrisă, ca la restaurant — fel, cantitate, preț, pe trei coloane:

- **Mâncare:** mici cu piure, ciorbă de burtă, cartofi prăjiți.
- **Deserturi:** papanași, înghețată.
- **De băut:** Prigat, bere, vin — măsurate în litri.

Comanda se face dintr-o dată, ca la masă: apeși pe câte feluri vrei și fiecare
se bifează — ciorbă, papanași și un suc, sau numai ciorba și sucul, cum ai chef.
Apăsat a doua oară, felul se șterge de pe listă. Jos e scris cât face tot.

La sfârșit alegi cum se încheie:

- **„doar atât"** — pleacă cu comanda și ia meniul cu el.
- **„mă mai gândesc"** — duce ce-ai ales până acum, dar îți lasă meniul în față;
  ce mai bifezi pleacă la următorul drum.
- **„nimic, mulțumesc"** — o opțiune ca oricare alta: dă din cap și își vede de
  tura lui.

Chelnerul duce comanda la bar; barmanul toarnă sau gătește la aragaz, cu fum și
sfârâit, felurile unul după altul. Se aude cum se umple paharul. Pe urmă vin
toate odată pe masă, fiecare la locul lui lângă cel care le-a cerut, și se
consumă din ele din când în când, fiecare cum îi e felul.

Vinul face excepție: vine cu sticla și cu paharul gol. Sticla stă pe mijlocul
mesei, la vedere — nu chiar în buric, ci în golul de lângă clovn, ca să nu-i
iasă prin cap; apeși pe ea și îți torni singur.

Partida merge înainte tot timpul. Detaliile, în [PLAN.md](PLAN.md), Faza 9.

## 4c. Adversarii

Draconianul și reptilianul își aleg singuri cărțile. Dacă pe calculator rulează
Ollama, sunt întrebați pe el — fiecare cu firea lui, dată de `promptSistem` și
`temperature` din `personaje.json`. Răspunsul se verifică întotdeauna: dacă nu e
o carte din mâna lui, se aruncă. Tot de acolo poate veni și conversația
musafirilor de la masa a treia.

Fără Ollama, sau dacă modelul întârzie ori spune o prostie, se joacă și se
vorbește după ce e scris în cod. Scena nu se oprește niciodată după un răspuns
care nu vine, și nu trebuie instalat nimic ca să meargă.

## 5. Reguli de implementare

- Un singur fișier: `index.html` (HTML + CSS + JS inline).
- Canvas 2D, personaje desenate din forme geometrice în cod — fără imagini.
- Cod și comentarii în română, pe secțiuni numerotate.
- Textul din scenă e doar meniul de comandă, valorile de pe cărți și vorbele
  musafirilor de la masa a treia. În rest, nicio literă.
- Canvas-ul ocupă tot ecranul și se redimensionează cu fereastra.
- Sunetul se sintetizează în cod cu WebAudio, ca și desenul: fără fișiere audio.
  Pornește la primul clic, fiindcă browserele nu lasă altfel, și rămâne un
  adaos — scena se înțelege și fără el.

## 6. Etape

1. Masa, lumina, cele trei locuri goale.
2. Personajele desenate static, fiecare la locul lui.
3. Animații de idle (respirație, legănat, clipit).
4. Ciclul de împărțit și jucat cărțile.
5. Reacțiile la câștig/pierdere, inclusiv focul draconianului.
6. Localul: masa cu scaune, barul, masa a doua.
7. Lumea din local: cei doi clienți, chelnerul, barmanul.
8. Comanda, bucătăria și sunetul.
9. Partida adevărată, jucată de privitor.

Ordinea de lucru efectivă e cea din [PLAN.md](PLAN.md).

## 7. În afara scopului

- Scor: se joacă mână după mână, fără să țină nimeni socoteala.
- Orice altă interacțiune în afară de jucat cărțile și comanda de la bar.
- Meniuri, setări, salvare.
