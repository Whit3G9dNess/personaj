# Personaj — Partida de cărți

Spec minimal. Trei personaje stau la o masă și joacă cărți.

## 1. Ce este

O scenă animată, într-un singur fișier `index.html`, desenată în canvas 2D și
sonorizată în WebAudio. Fără biblioteci, fără build, fără fișiere externe — nici
imagini, nici sunete: se deschide direct în browser.

Cărțile au valori de la 1 la 10 și cea mai mare ia levata. O mână ține **trei
levate**: se împart trei cărți și se joacă toate, una pe levată, abia apoi se
împart altele — de aici vine singura întrebare care contează, *dau zecele acum,
sau îl țin?* Cărțile se pun **cu fața în jos** și se întorc toate odată, deci
nimeni nu știe ce vine.

**Mâna clovnului o joci tu**: o vezi jos, în fața mesei, și alegi ce pune pe
masă. Ceilalți doi joacă singuri. Dacă nu alegi nimic, joacă și el după
douăsprezece secunde — scena rămâne bună și dacă doar te uiți la ea.

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

### 2.4 Dealerul
Om, nu personaj de basm: cămașă albă, vestă închisă la culoare, papion. Stă pe
latura din stânga și nu joacă — el împarte cărțile și întoarce cartea mesei.
Din când în când zice o vorbă și intră și el într-o mână, dar rar.

## 3. Scena

Cei trei joacă la o masă dintr-un bar.

- Masă pătrată cu blat gros și picior în X, văzută ușor de sus, într-o lumină
  caldă de deasupra. Cei trei stau pe scaune, la masă.
- Masa are patru laturi și fiecare latură are pe cineva: în față clovnul (cu
  spatele la noi), în spate draconianul, în dreapta reptilianul, iar în stânga
  **dealerul**. Camera e fixă.
- În centru: teancul de cărți și mâna jucată curent.
- În fundal, de-a latul scenei: barul — tejghea cu panou verde, două polițe
  luminate cu sticle, lămpi atârnate deasupra, plante și scaune înalte trase la
  tejghea. În spatele ei sunt trei zone, fiecare cu omul ei: **chiuveta** în
  stânga de tot, cu spălătorul de vase; **robinetele** la mijloc, cu barmanul,
  care face numai de băut; **bucătăria** în dreapta de tot — aragaz și masă de
  amestecat —, cu bucătarul. Nimeni nu face treaba altuia.
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

Se împart trei cărți fiecăruia (animație de distribuire, câte una pe rând), apoi
se joacă **trei levate** din ele. O levată ține așa:

1. Fiecare personaj „se gândește" — un timp diferit pentru fiecare.
2. Fiecare pune o carte pe masă, **cu fața în jos**, în ordine. Când e rândul
   clovnului, masa te așteaptă pe tine.
3. Când sunt toate jos, se **întorc pe rând** și se vede ce a jucat fiecare.
4. Cea mai mare carte ia levata; câștigătorul trage cărțile spre el și deschide
   levata următoare.
5. Reacții: clovnul exagerează, draconianul scoate foc, reptilianul tace.
6. Pauză scurtă, apoi urmează levata următoare.

După a treia levată se vede cine a luat mai multe: ăla a câștigat mâna, iar cel
cu cele mai puține o pierde — atunci clovnul mătură cărțile de pe masă în aer.
Pe urmă se împart cărți noi și se ia de la capăt.

**Jokerul.** Cam la fiecare a treia mână, una dintre cărțile împărțite e un
joker: n-are cifră, are căciula bufonului. El **bate zecele**, dar pierde în
fața oricărei alte cărți. Fiind mai mulți la masă, regula se spune așa ca să nu
se învârtă în cerc: jokerul ia levata **doar dacă pe masă e un zece**; dacă nu
e, el e cea mai mică de acolo. Așa zecele redevine riscant, și apare cacealmaua.

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

Chelnerul duce comanda la bar, la omul ei: băutura o toarnă barmanul, la mijloc,
cât să se umple un pahar; mâncarea o face bucătarul, în dreapta, cu fum și
sfârâit — și durează, fiindcă se gătește și se amestecă: **20 de secunde un fel
de mâncare, 15 un desert**. Felurile se fac unul după altul, apoi vin toate
odată pe masă, fiecare la locul lui lângă cel care le-a cerut, și se consumă din
ele din când în când, fiecare cum îi e felul.

Ce s-a golit nu rămâne pe masă: când n-are altceva de făcut, chelnerul trece,
strânge vasele goale și le duce la chiuveta din stânga barului, unde le spală
spălătorul de vase. Masa se curăță singură.

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

## 4d. Vorba cu ei

În colțul din dreapta-jos e un balon de vorbă. Îl apeși și se deschide o
fereastră de chat: alegi din listă pe cine întrebi — sau dai clic direct pe el
în scenă — și scrii.

Poți vorbi cu oricine e în local: draconianul, reptilianul, dealerul, chelnerul,
barmanul, bucătarul, spălătorul de vase, cei doi de la șah și musafirii de la
masa a treia, cât stau acolo. Cu clovnul nu, fiindcă el ești tu.

Răspunde fiecare cum îi e firea, iar ce spune se aude și în local: îi apare
deasupra capului, ca vorbele de la masa a treia. Răspunsurile le scrie modelul
din Ollama; fără el, fiecare are câteva vorbe ale lui, scrise în cod.

Partida merge înainte cât ții fereastra deschisă.

## 5. Reguli de implementare

- Un singur fișier: `index.html` (HTML + CSS + JS inline).
- Canvas 2D, personaje desenate din forme geometrice în cod — fără imagini.
  Singura excepție e fereastra de vorbă: pentru scris e nevoie de o casetă
  adevărată, cu tastatură cu tot. Stă tot în `index.html` și e îmbrăcată în
  culorile localului, iar scena merge mai departe și dacă lipsește.
- Cod și comentarii în română, pe secțiuni numerotate.
- Textul din scenă e doar meniul de comandă, valorile de pe cărți, vorbele
  musafirilor de la masa a treia și vorba cu cine întrebi tu. În rest, nicio
  literă.
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
