# Personaj — Partida de cărți

Spec minimal. Trei personaje stau la o masă și joacă cărți.

## 1. Ce este

O scenă animată, într-un singur fișier `index.html`, desenată în canvas 2D și
sonorizată în WebAudio. Fără biblioteci, fără build, fără fișiere externe — nici
imagini, nici sunete: se deschide direct în browser.

Cărțile se împart și se joacă *decorativ* — ritmul e credibil, dar nu există reguli
reale de joc. Interesul e în personaje și în cum reacționează unele la altele.

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
- În jur, în penumbră: barul cu sticle de sucuri, o a doua masă cu doi clienți
  și un chelner care îi servește. Nu se desenează pereți — barul și masa a doua
  ies din întuneric doar cât să se înțeleagă unde suntem.
- Masa celor trei rămâne singura complet luminată. Tot ce e în jur e mai
  întunecat, mai mic și mai puțin detaliat, ca să nu fure atenția.

## 4. Ciclul unei mâini

1. Se împart cărțile (animație de distribuire, câte una pe rând).
2. Fiecare personaj „se gândește" — un timp diferit pentru fiecare.
3. Fiecare pune o carte pe masă, în ordine.
4. Unul câștigă mâna și trage cărțile spre el.
5. Reacții: clovnul exagerează, draconianul scoate foc, reptilianul tace.
6. Pauză scurtă, apoi se reia de la 1.

Bucla merge la nesfârșit, fără input din partea privitorului.

## 4b. Comanda de la bar

Singura interacțiune din scenă. Comanzi pentru oricare dintre cei trei — suc,
bere sau vin. Chelnerul se duce la bar, se aude cum se toarnă, aduce paharul și
îl pune pe masă; după aceea se bea din el din când în când, fiecare cum îi e
felul. Partida merge înainte tot timpul. Detaliile, în [PLAN.md](PLAN.md),
Faza 9.

## 5. Reguli de implementare

- Un singur fișier: `index.html` (HTML + CSS + JS inline).
- Canvas 2D, personaje desenate din forme geometrice în cod — fără imagini.
- Cod și comentarii în română, pe secțiuni numerotate.
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
6. Localul: masa cu scaune, tejgheaua cu sucuri, masa a doua.
7. Lumea din local: cei doi clienți și chelnerul.
8. Comanda de la bar, cu sunet.

Ordinea de lucru efectivă e cea din [PLAN.md](PLAN.md).

## 7. În afara scopului

- Reguli reale de joc și scor.
- Orice altă interacțiune în afară de comanda de la bar (§4b).
- Meniuri, setări, salvare.
