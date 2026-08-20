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
| 9 | Comanda, bucătăria și sunetul | ✅ gata |
| 10 | Partida adevărată: joci tu cărțile clovnului | ✅ gata |
| 11 | Partida cu cap: trei levate, cărți întoarse, jokerul | ✅ gata |
| 12 | Localul reașezat: masă pătrată, dealer, bucătar, chiuvetă | ✅ gata |
| 13 | Vorba cu ei: chat cu oricine din local | ✅ gata |
| 14 | Pomenit pe altul: `@cineva` în vorbă | ✅ gata |
| 15 | Vorbă curată, ținută minte, și `@tine` | ✅ gata |
| 16 | Mai mulți odată, și cine se bagă în vorbă | ✅ gata |
| 17 | Vorbă pe înțeles: temă, ce ține minte, întrebări înapoi | ✅ gata |
| 18 | Fără repetat, strigat pe nume, legat de dinainte | ✅ gata |
| 19 | Când te iei de ei | ✅ gata |
| 20 | Furia ținută minte, și vorba dintre ei | ✅ gata |
| 21 | Masa are un capăt | ✅ gata |
| 22 | Intrarea în local | ✅ gata |

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
  cu o mână de argint, celălalt e făcut tot din aur, din cap până-n picioare.
  Mută pe rând, la câteva secunde, fără reguli adevărate — ca și cărțile de la
  masa noastră.

  Amândoi își desenează singuri brațele, căzute din umăr, cu mâinile pe
  marginea blatului. Prima variantă le punea mâinile ca pe niște puncte
  plutind pe lângă corp, iar omului de aur îi mai dădea și două luminițe
  răzlețe — de departe arătau ca niște particule agățate de el. Un test cere
  acum ca nicio formă de-a lui să nu iasă din trupul lui.
- **Chelnerul**, care se plimbă prin local cu tava: pe la bar, pe la masa a
  doua, și pe la masa noastră din când în când. Nu așteaptă să fie chemat —
  face turul lui oricum. Se oprește puțin la fiecare popas, apoi merge mai
  departe. Costum clasic alb-negru, șorț lung, mustață neagră, păr brunet.
- **Barmanul**, care se plimbă în spatele tejghelei ștergând un pahar: cămașă
  albă cu mânecile suflecate, bretele, papion, barbă. Nu iese niciodată din
  spatele barului.
- **A treia masă**, în față-stânga, goală mai tot timpul. Din când în când intră
  doi inși, se așază, schimbă câteva vorbe scrise în bule deasupra lor, și
  pleacă. Vorbesc pe rând, niciodată amândoi odată.

  **Nu sunt aceiași de fiecare dată:** la fiecare vizită se croiesc alți doi din
  liste de haine, piele, păr și un accesoriu (pălărie, fular, ochelari sau
  nimic), cu mici diferențe de statură. Un test cere ca din patru vizite să iasă
  cel puțin două înfățișări deosebite.

  **Paharele nu apar din senin.** Sticla de vin e pe masă cum s-au așezat — e a
  mesei, nu a lor —, dar paharele apar abia când trece chelnerul pe la ei; are
  un popas al lui acolo.

  **Vorbesc despre ce văd**, inclusiv despre masa noastră: focul draconianului,
  liniștea reptilianului, cărțile aruncate ale clovnului, comanda care tocmai a
  sosit. Regula veche („fundalul nu știe de partidă") a căzut aici, la cerere.

Contractul e cel de la masa mare (`deseneaza`, `actualizeaza`), ca să nu apară
un al doilea fel de a face un personaj. Nu primesc evenimente de la mașina de
stări a jocului; ce află despre partidă, află uitându-se — prin `vizita.vede`.

**Conversația poate veni de la model.** Dacă Ollama e pornit, musafirii primesc
de acolo următoarele două replici, cu ce și-au spus până atunci ca fir. Dacă nu
răspunde în cinci secunde, rămân replicile scrise de mână, și nu se vede
diferența decât prin faptul că se repetă mai des.

Toți patru sunt croiți pe același trup de bază, fiindcă sunt fundal: nu trebuie
să fie cineva anume. Fiecare desenează peste el ce are în plus — mâna de aur,
șorțul și tava, bretelele și paharul.

Adâncimea o dă tot ordinea desenării, ca la Faza 7: barmanul e prins între raft
și tejghea, cei doi șahiști între piciorul mesei și blat, iar chelnerul se
desenează în trei locuri diferite, după cât de aproape e de noi — în spatele
mesei a doua, în spatele mesei noastre, sau în fața ei. Fără al treilea caz,
când trecea pe lângă masa a doua părea că s-a urcat pe ea.

Mișcarea dintr-un loc în altul nu mai e a cărților: `mutaObiect` /
`actualizeazaObiect` mută la fel și piesele de șah.

Nici mersul nu mai e al fiecăruia: `mergiSpre(p, x, y, viteză, dt)` duce pe
oricine, cu pas constant și cu picioarele mișcându-se. Prima variantă a
barmanului se apropia de țintă cu un procent din distanță pe cadru — pornea
brusc, aluneca fără să pășească și părea că se teleportează înapoi după ce
termina de gătit. Un test cere acum ca nimeni să nu se mute mai mult de 0,6U
într-un cadru.

**Verificare:** localul e viu, dar dacă te uiți la masa celor trei, nimic din ce
se întâmplă în spate nu te trage cu ochiul.

**Rămas de aici:** reptilianul era prea mic față de ceilalți doi și a fost mărit
la 13×25U — acum e al doilea ca mărime, după draconian.

## Faza 9 — Comanda, bucătăria și sunetul

Aici scena capătă primul ei buton, și primul sunet. Vine după tot restul
fiindcă are nevoie de el: de chelner ca să aducă, de bar ca să toarne, de masă
ca să pună paharul pe ea.

> Faza asta îl pune pe barman să facă și de băut, și de mâncare. La **Faza 12**
> barul se împarte în trei, iar mâncarea trece la bucătar; ce scrie mai jos
> despre aragazul barmanului e cum a fost, nu cum e acum.

1. **Chemi chelnerul.** Dai clic pe personajul pentru care vrei să comanzi, iar
   chelnerul întrerupe tura și vine la masa noastră. Până nu-l chemi, scena
   merge exact ca până acum.
2. **Alegi** — băutură (suc, bere, vin), mâncare (mici cu piure) sau **nimic**.

   Se alege ca la masă, nu ca dintr-un meniu de calculator: bifezi câte feluri
   vrei odată — ciorba, papanașii și sucul —, iar a doua apăsare pe același fel
   îl scoate de pe listă. Comanda se încheie cu **„doar atât"** (pleacă cu ea și
   ia meniul) sau cu **„mă mai gândesc"** (duce ce-ai ales și îți lasă meniul în
   față, pentru încă un rând). Cât e meniul pe masă, cărțile rămân ale tale:
   clicurile care nu cad pe meniu ajung tot la mâna ta.

   Refuzul e o opțiune la fel de bună ca celelalte, nu o ieșire de avarie: dacă
   alegi „nimic, mulțumesc", chelnerul dă din cap și își vede de tura lui. La
   fel se întâmplă și dacă închizi meniul fără să alegi. Scena trebuie să fie
   la fel de bună neatinsă — asta e starea ei normală, la urma urmei.
3. **Chelnerul duce comanda la bar.**
   Chelnerul așteaptă acolo până vine și barmanul: nu se toarnă și nu se
   gătește nimic până nu ajunge omul la locul lui.
   - **De băut:** barmanul toarnă. Se **aude turnatul**, altfel pentru fiecare:
     sucul scurt și subțire, berea cu spumă care sfârâie după, vinul mai gros și
     mai grav. Sunetul urcă în înălțime pe măsură ce paharul se umple — de asta
     se aude că e un pahar, nu un zgomot oarecare.
   - **De mâncare:** barmanul se mută la **aragazul** de la capătul barului,
     pune micii pe plită, iar de acolo iese **fum** cât timp lucrează (același
     sistem de particule ca focul draconianului). Se aude sfârâitul. Când sunt
     gata, pune piureul pe farfurie și o decorează.
4. **Aduce comanda** la masă — tot ce e pe tavă odată — și o pune lângă cel care
   a cerut-o, cu un „poc" scurt de sticlă sau de farfurie pe lemn. Sticla de vin
   face excepție: ea se așază pe mijlocul mesei, în golul din dreapta clovnului
   — în buricul mesei ar ieși prin capul lui, fiindcă el stă între noi și blat.
   Dacă ai mai cerut ceva cât era plecat, se întoarce la bar cu comanda aia.
5. **Se consumă din când în când**, fiecare cum îi e felul: clovnul ridică
   paharul teatral și soarbe zgomotos, draconianul îl dă pe gât dintr-o dată,
   reptilianul ia o înghițitură mică și pune paharul exact unde era. Nivelul din
   pahar scade, farfuria se golește. Când s-a terminat, poți comanda din nou.

Comanda nu oprește partida: cărțile se împart și se joacă mai departe cât timp
chelnerul e pe drum. Dacă mâna se termină între timp, cu atât mai bine — se
suprapun.

Barmanul capătă astfel o treabă adevărată: până acum doar șterge un pahar și se
plimbă. Aragazul, cu hota și plita lui, se adaugă la capătul barului, pe blatul
de lucru care există deja.

## Faza 10 — Partida adevărată

Până aici cărțile se joacă decorativ: n-au valori, iar câștigătorul e ales la
zaruri. Faza asta schimbă lucrul ăsta — **tu joci cărțile clovnului**, ceilalți
doi joacă împotriva ta.

- Cărțile capătă valori, iar mâna se câștigă după o regulă simplă și vizibilă
  (cea mai mare carte ia mâna).
- Mâna clovnului se vede — doar ție, întoarsă spre tine, jos în fața mesei — și
  alegi tu ce pune pe masă. Până alegi, masa te așteaptă, cu o lumină caldă sub
  cărțile tale. Dar nu la nesfârșit: după 12 secunde joacă el, ca scena să
  rămână bună și pentru cineva care doar se uită.
- Draconianul și reptilianul aleg singuri, fiecare după firea lui: draconianul
  aruncă ce-i vine, reptilianul calculează.

**Adversarul.** Pe calculatorul ăsta e instalat Ollama, cu `gemma4:e2b`, deci
ceilalți doi pot fi conduși de un model adevărat: primesc situația mesei și
răspund cu ce carte joacă. Fiecare are în `index.html` un `promptSistem` scurt —
altul decât `fire`, care e pentru vorbă —, iar cât de imprevizibil joacă iese
din `haos`: `temperature` e `0.6 + haos`. Convenția e scrisă și în
`personaje.json`, la `implicit`.

Regula de aur: **dacă Ollama nu răspunde, scena merge mai departe.** Adversarii
au și o judecată scrisă în cod, simplă, și pe ea cad înapoi dacă modelul lipsește,
întârzie sau răspunde aiurea. Nimeni nu trebuie să instaleze nimic ca să se uite
la scenă.

### Ce am aflat despre Ollama, pe drum

Trei lucruri, toate găsite încercând, nu ghicind:

1. **`think: false` nu e opțional.** Cu gândirea pornită, `gemma4:e2b` a luat
   30 de secunde și a răspuns gol — tot bugetul se dusese pe gândit. Fără ea,
   răspunde în 1,0–1,4 secunde la cald.
2. **Modelul spune des cărți pe care nu le are.** Întrebat ce joacă având în
   mână 3, 7 și 9, a răspuns „1", apoi „2". De asta răspunsul se verifică
   întotdeauna: dacă valoarea nu e în mâna lui, se aruncă și joacă judecata din
   cod. Nu e o măsură de siguranță teoretică — se întâmplă des.
3. **Modelul pierde cursa cu timpul de gândire al scenei.** Draconianul se
   gândește 1–2 secunde, cât îi ia și modelului să răspundă. Dacă a fost
   întrebat și încă tace, masa îi mai dă 1,6 secunde; după aceea joacă din cod.
   Așa, în timp real, ajunge ca amândoi adversarii să fie conduși de model.

**Ce s-a schimbat în SPEC:** §1 zicea că partida e decorativă și §7 punea
regulile de joc în afara scopului. Amândouă s-au schimbat. A rămas în afara
scopului scorul: se joacă mână după mână, fără să țină nimeni socoteala.

Pe un calculator fără Ollama nu se schimbă nimic vizibil: adversarii joacă din
cod, la fel de repede. E scris și în README, nu ascuns.

### Sunetul

Sunetele se fac în cod, cu WebAudio, exact cum se fac și personajele: din forme
simple, fără niciun fișier. Zgomot filtrat pentru turnat, un filtru care urcă
pentru paharul care se umple, click-uri scurte pentru bule și pentru pahar.

Un browser nu lasă sunetul să pornească nechemat, deci contextul audio se
deschide la primul clic — care e chiar clicul de comandă. Până atunci, scena e
mută, așa cum e și acum.

**Meniul** e o listă scrisă, ca la restaurant: capete de listă (Mâncare,
Deserturi, De băut), iar sub ele felul, cât e porția și cât costă, în trei
coloane aliniate. Fără desene — la nouă feluri ar fi fost cinci pahare care se
bat cap în cap.

Sucul e Prigat, mâncarea e mici cu piure, ciorbă de burtă și cartofi prăjiți,
deserturile papanași și înghețată. Băuturile se măsoară în litri, mâncarea în
grame sau mililitri — un test verifică chiar asta, pe fiecare fel în parte.

**Fiecare fel arată a el.** Ciorba vine în castron adânc, cu zeamă galbenă și
bucăți în ea; cartofii sunt nouă bețe aurii aruncate pe farfurie; papanașii au
gogoașa, căciulița, smântâna și dulceața; înghețata vine în cupă cu picior, cu
trei bile și fulgi de ciocolată. La început toate cinci se desenau la fel — o
farfurie cu mici și piure —, iar un test cere acum ca oricare două feluri să nu
aibă exact aceleași culori.

**Lucrurile de pe masă nu se mai suprapun:** fiecare comandă nouă primește locul
ei pe blat, lângă cel care a cerut-o, la 0,38 radiani de precedenta.

**Vinul vine cu sticla lui.** Paharul sosește gol; apeși pe sticlă și îți torni
singur, cu tot cu sunetul de turnat. O sticlă ține trei pahare.

Textul din meniu e primul din toată scena. Până acum n-a existat nicio literă în
ea, în afară de numele-ajutor de la siluete, care au dispărut la Faza 5.

**Verificare:** chemi chelnerul, comanzi o bere, o auzi turnată și, peste puțin
timp, cel pentru care ai comandat are un pahar din care soarbe. Ceri mici cu
piure și vezi fumul ieșind de la aragaz cât se fac. Partida n-a stat nicio clipă.

---

## Faza 11 — Partida cu cap

Până acum se împart trei cărți, se joacă una și celelalte două se aruncă. Faza
asta le pune pe toate trei în joc, ca să fie ceva de hotărât, nu doar de apăsat.

**1. Toate trei levatele, apoi se împart altele.** O mână are trei levate: fiecare
pune pe rând câte o carte, cea mai mare ia levata, iar cine a luat-o deschide
levata următoare. Abia după a treia se împart cărți noi. De aici vine singura
întrebare care contează: *dau zecele acum, sau îl țin?*

**2. Cărțile se pun cu fața în jos și se întorc toate odată.** Fiecare pune la
rândul lui, ca până acum — gândirea pe rând rămâne —, dar cartea stă cu spatele
în sus până cad toate trei. Atunci se întorc deodată și se vede cine a luat
levata. Fără asta, cel care joacă ultimul știe tot, iar cel care deschide nu
poate păcăli pe nimeni. Cărțile știu deja să stea întoarse: `fataInSus`.

**3. Jokerul.** Singura carte specială din tot jocul, în locul unei cărți
obișnuite, o dată la vreo trei mâini — uneori ție, uneori lor.

- Jokerul **bate zecele**.
- În fața oricărei alte cărți, **pierde**.
- Fiindcă suntem trei la masă și altfel se învârte în cerc (joker bate 10, 10
  bate 7, 7 bate joker): jokerul ia levata **doar dacă pe masă e un zece**;
  dacă nu e niciun zece, el e cea mai mică carte de pe masă.

Așa, zecele redevine riscant și apare cacealmaua: arunci jokerul sperând că
cineva își dă zecele tocmai în levata aia.

**4. Cine pierde mâna face cinste.** Personajul care a luat cele mai puține
levate comandă singur ceva pentru masă. Așa se leagă partida de restaurant prin
poveste, nu prin monede.

**5. Ce-au băut și mâncat le schimbă firea.** Draconianul după două beri joacă și
mai la nimereală, reptilianul cu burta plină se gândește mai mult. Se vede din
cum joacă, nu dintr-o cifră pe ecran. Se leagă de `fire` și de `haos`, cele din
fișa lor din `personaje.json`.

Ordinea de lucru: **1** întâi, singură — schimbă cel mai mult și nu strică nimic
din ce merge. Apoi **2** și **3**, care merg împreună: fără cărți întoarse,
jokerul e mort de fiecare dată când deschizi tu levata, fiindcă ceilalți îl văd
și nu-și mai dau zecele. **4** și **5** sunt condimente, se pun oricând mai
încolo.

**Stadiu: toate cinci sunt gata.** Ce s-a mai văzut pe drum, scris aici ca să nu
se redescopere:

- Cărțile întoarse schimbă și judecata adversarilor: până acum reptilianul se
  uita la ce e pe masă și punea cea mai mică dintre cele care bat. Cu cărțile cu
  fața în jos ar fi trișat, așa că acum joacă din ce are în mână și din a câta
  levată e — își ține tăria pentru ultima. Tot de-aia modelului nu i se mai
  spune ce s-a jucat, doar câți au pus înaintea lui.
- Clovnul își arunca cărțile în aer când pierdea. Cu trei levate, ar fi rămas
  fără ce juca după prima — acum gestul e al sfârșitului de mână și mătură
  cărțile de pe masă, nu pe cele din mâna lui.
- Reacțiile mari („castigaMana", „pierdeMana") se trimit după a treia levată,
  celui cu cele mai multe și celui cu cele mai puține. De aici s-a legat și
  punctul **4**: cine pierde mâna face cinste.

**Cum s-au făcut 4 și 5, la urmă:**

- **Cinstea** e o comandă ca oricare, doar că n-o dai tu: cel cu cele mai puține
  levate cheamă chelnerul, iar chelnerul nu-i mai întinde meniul — știe ce dă.
  Fiecare dă ce bea el (`CINSTEA`, oglindită în `personaje.json`): draconianul
  bere, reptilianul vin, dealerul bere, clovnul suc. E un **rând pentru toată
  masa**, deci comanda a căpătat `catreCine`: cui i se pune fiecare lucru de pe
  tavă, altfel toate paharele ar fi ajuns în dreptul unuia singur.
  Se lasă păgubaș dacă e o comandă pe drum, dacă ai meniul în față sau dacă
  chelnerul e ocupat — un rând de cinste nu se bagă peste ce ai cerut tu.
- **Ce-au băut** nu e un număr pe ecran, ci două cifre pe personaj — `baute` și
  `mancate` — și trei funcții prin care trec toate deciziile: `haosulLui`,
  `gandireaLui`, `betia`. Un pahar cu alcool golit adaugă haos (până la trei
  pahare), o farfurie golită îngreunează gândirea (până la două). Se scurg de la
  sine: un minut și jumătate băutura, ceva mai mult mâncarea. Sucul nu îmbată.
- Două lucruri au ieșit la iveală abia acum, fiindcă până aici chelnerul venea la
  masă doar când îl chemai tu: la plecare își relua tura tăind peste blat (acum
  pleacă prin ocolul din stânga, `pleacaDeLaMasa`), iar bula de deasupra capului
  se stingea numai pentru cei cu care se poate vorbi — clovnul n-avea cine să-i
  scadă timpul, deși acum are și el ce spune.

**Rămâne pe dinafară:** jetoane, mize, cash-out, scor scris pe ecran, magazin,
mâncare care dă bonusuri la cărți, alte cărți speciale în afară de joker, 3D.
Toate cer cifre pe ecran, ecrane de ales și salvare — adică fix ce spune SPEC §7
că nu face scena asta. Dacă vrem jocul acela, e alt fișier, nu ăsta.

**De hotărât mai încolo:** dacă se împart 4 cărți din care arunci una și joci cu
3. Ar da o decizie chiar la împărțire, dar întâi vedem cum se simte cu 3.

**Verificare:** o mână ține trei levate; dacă ai jucat zecele în prima, în a
treia te descurci cu ce ți-a rămas. Cărțile stau întoarse până cad toate trei.
Un joker jucat peste un zece ia levata; același joker, când pe masă nu e niciun
zece, n-o ia.

---

## Faza 12 — Localul reașezat

Scena s-a înghesuit: masa e mică, mijlocul ei e acoperit de capul clovnului, iar
barmanul face și de băut, și de mâncare, și tot pe masă rămâne totul până se
plictisește privitorul. Faza asta desface localul pe zone, ca într-un local
adevărat.

**1. Masa: pătrată, mai mare, mutată spre dreapta.** Blat gros pătrat, cu picior
în X, văzut tot ușor de sus. Mutată la dreapta, ca să rămână loc în stânga
pentru dealer și pentru restul localului.

**2. Patru locuri, nu trei.** Clovnul rămâne în față (mâna lui e mâna ta),
draconianul trece pe latura din spate, iar reptilianul pe cea din dreapta. Locul
din stânga e al **dealerului**: el împarte și, mai încolo, întoarce cartea mesei.
De obicei nu joacă — dar cam la una din șapte mâini zice „hai că joc și eu o
rundă" și intră și el, niciodată de două ori la rând.

**3. Barul se împarte în trei.** De la stânga la dreapta:

| Loc | Cine | Ce face |
|-----|------|---------|
| stânga de tot | spălătorul de vase | chiuveta: spală ce aduce chelnerul |
| mijloc | barmanul | numai de băut: sucuri, bere, vin |
| dreapta de tot | bucătarul | aragazul și zona de amestecat: mâncare și deserturi |

**4. Vasele se strâng.** Când s-a terminat mâncarea din farfurie sau băutura din
pahar, vine chelnerul, le ia de pe masă și le duce la chiuvetă. Masa rămâne
curată de la sine, fără să apeși nimic.

**5. Mâncarea se face mai încet.** Aragazul și amestecatul cer timp: **20 s**
pentru un fel de mâncare, **15 s** pentru un desert, băutura rămâne scurtă.

**De hotărât:** ce regulă are cartea întoarsă de dealer — „atu" (cifra ei bate
orice în mâna aia) sau „pe dos" (dacă e pară, cea mai mică ia levata).

**Verificare:** masa e pătrată și are patru inși în jur; comanzi mici cu piure și
se face la dreapta, la bucătar, nu la mijloc; termini paharul și, peste puțin
timp, nu mai e pe masă — e la chiuvetă, în stânga.

---

## Faza 13 — Vorba cu ei

Până acum nu poți spune nimic nimănui: comanzi de la meniu și joci cărți, atât.
Faza asta adaugă **o iconiță de chat în dreapta-jos**; o apeși, se deschide o
fereastră de vorbă și poți vorbi cu oricine din local — barmanul, draconianul,
reptilianul, dealerul, chelnerul, bucătarul, spălătorul, cei doi de la șah și
musafirii de la masa a treia.

1. **Iconița.** Un balon de vorbă în colțul din dreapta-jos. Apeși pe el, se
   deschide fereastra; apeși din nou (sau pe „x"), se închide. Cât e închisă,
   scena arată exact ca înainte.
2. **Cu cine vorbești.** În fereastră, sus, e lista celor din local. Alegi unul
   și vorbești cu el; cât e fereastra deschisă, poți alege altul dând clic pe el
   direct în scenă.
3. **Cum scrii.** Într-o casetă adevărată de HTML, așezată peste scenă și
   îmbrăcată în culorile localului. E singurul lucru din toată scena care nu e
   desenat în canvas — pentru scris ai nevoie de tastatură adevărată, cu
   diacritice și lipit de text cu tot. Regula „un singur fișier" rămâne: caseta
   stă tot în `index.html`, cu stilul lângă ea.

   Scena trebuie să meargă și fără casetă: dacă lipsește (cum se întâmplă la
   teste, unde rulează pe o pânză falsă), vorba funcționează mai departe în cod,
   doar că n-o vede nimeni scrisă.
4. **Cine răspunde.** Modelul din Ollama, fiecare cu firea lui: draconianul și
   reptilianul o au deja (clovnul nu — pe el îl joci tu), ceilalți primesc câte
   una scurtă, toate strânse în `FIRI` și oglindite în `personaje.json`. Răspunsul apare în fereastră și, scurtat, și deasupra capului lui în
   scenă, ca vorbele de la masa a treia.
5. **Fără Ollama.** Regula de aur rămâne: dacă modelul lipsește sau tace, fiecare
   are câteva răspunsuri scrise de mână, potrivite cu firea lui. Nu se blochează
   nimic și nu apare niciun mesaj de eroare — pur și simplu răspund mai scurt.

**Partida merge înainte** cât ții fereastra deschisă, ca la meniu: cărțile rămân
ale tale, iar dacă îți vine rândul și nu joci, joacă el după douăsprezece
secunde.

**Rămâne pe dinafară:** vorbă între ei doi pe subiectul tău, ținut minte de la o
deschidere a paginii la alta, comenzi date prin chat („adu-mi o bere") — comanda
rămâne la meniu. (Ținutul minte *cât ține pagina* vine la Faza 15.)

**Verificare:** apeși iconița, alegi barmanul, scrii „ce mai faci?" și îți
răspunde el, nu draconianul. Închizi fereastra și scena e neatinsă.

---

## Faza 14 — Pomenit pe altul

Vorbești cu barmanul și vrei să-l întrebi de draconian. Acum n-ai cum: modelul
primește doar firea celui întrebat, iar „ăla mare și verde" nu-i spune nimic.
Faza asta adaugă **pomenirea**: scrii `@draconian` (sau `/draconian`) în vorbă și
cel întrebat știe despre cine e vorba.

1. **Cum scrii.** Un `@` sau un `/` și numele: *„barmanule, ce crezi de
   @draconian?"*. Semnul deschide o listă mică deasupra casetei, cu cine e în
   local; scrii mai departe și lista se strânge, apeși pe unul (sau Tab, sau
   Enter) și numele se completează singur. Cât e lista deschisă, Enter alege din
   ea, nu trimite vorba.
2. **Pe cine poți pomeni.** Pe oricine are nume în local, plus pe tine: clovnul
   n-are fire, fiindcă pe el îl joci tu, dar poate fi vorbit de rău. Numele merge
   scris fără diacritice și scurtat — `@barman`, `@aur`, `@spalator` — și de
   aceea fiecare primește câteva porecle scrise lângă fire.
3. **Ce primește modelul.** Pe lângă firea celui întrebat, o singură propoziție
   despre cel pomenit — cine e, văzut din afară — și îndemnul să-și spună
   părerea. Pomenirea se curăță din întrebare: modelul citește „ce crezi de
   Draconianul?", nu semnul.
4. **Fără Ollama.** Regula de aur: fiecare primește și câteva păreri scrise de
   mână, cu un gol în loc de nume — *„Cu {cine} nu mă pun"* —, ca răspunsul să
   fie tot despre cine ai întrebat, nu o vorbă la nimereală din rezervă.
5. **Ce se vede.** Numele pomenit rămâne scris aprins în fereastră, ca să se
   vadă la cine te-ai referit.

**Datele.** `despre`, `porecle` și `pareri` intră în `FIRI`, lângă fire, și se
oglindesc în `personaje.json`. Clovnul capătă și el intrare, cu `fire: null` —
`vorbitori()` cerne după fire, nu după intrare, ca să nu ajungi să vorbești cu
tine însuți.

**Rămâne pe dinafară:** cel pomenit nu află că s-a vorbit de el și nu se întoarce
spre masă; pomenirea nu-l cheamă în discuție. O pomenire pe vorbă, prima.

**Verificare:** îi scrii barmanului „ce crezi de @draconian?" și răspunsul e
despre draconian; fără Ollama, tot despre el e, nu o replică oarecare.

---

## Faza 15 — Vorbă curată, ținută minte, și `@tine`

Trei lipsuri ale ferestrei de vorbă, luate odată.

### 1. Ce răspund trebuie să fie o vorbă întreagă, în românește

Un model mic scapă des lucruri care n-au ce căuta în bulă: își scrie numele în
față („Barmanul: ce-ți torn?"), pune ghilimele, presară asteriscuri și mișcări
scrise între paranteze, se oprește la jumătatea cuvântului fiindcă i s-au
terminat silabele, sau alunecă în altă limbă.

Răspunsul trece de-acum printr-o **sită**, înainte să ajungă în fereastră:
numele din față, ghilimelele, asteriscurile și parantezele se scot; se ține doar
primul rând; dacă vorba s-a rupt la mijloc, se taie la ultima propoziție
încheiată. Ce rămâne se cântărește: dacă e goală, dacă are litere care nu sunt
din alfabetul nostru, sau dacă doar îți repetă întrebarea, se aruncă și
răspunde din firea lui, ca și cum modelul ar fi tăcut.

**Ce nu poate face sita:** un model mic tot va greși acordul din când în când.
Sita scoate gunoiul și aruncă vorba stricată de tot; gramatica ei o cere doar
îndemnul din prompt („scrii românește corect, cu diacritice"). Cine vrea vorbă
fără greșeli are nevoie de un model mai mare, nu de mai mult cod aici.

### 2. Ține minte ce i-ai spus

Acum, cum schimbi omul, se șterge tot. De-acum:

- **Numele tău.** Dacă scrii „numele meu este Alex", „mă cheamă Alex" sau „eu
  sunt Alex", scena reține *Alex* și fiecare îl primește în prompt: „cel cu care
  vorbești se numește Alex". Localul e mic, deci îl află toți, nu doar cel căruia
  i-ai spus.
- **Ce v-ați spus.** Fiecare își ține firul lui de vorbă. Te muți la altul și te
  întorci — vă găsiți unde ați rămas, nu de la zero.
- **Fără Ollama**, când te prezinți, tot îți răspunde ca lumea: are câteva vorbe
  de întâmpinare, cu numele tău în ele.

Nimic nu trece dintr-o deschidere a paginii în alta: la refresh, localul te uită.

### 3. `@tine`, și pe cel cu care vorbești nu-l mai poți pomeni

Nu are rost să-i scrii draconianului `@draconianul` — el e chiar acolo. De-aceea
**cel cu care vorbești iese din listă**, iar în locul lui apare `@tine`: *„ce
crezi despre @tine?"*, și își spune părerea despre el însuși. Dacă totuși îi
scrii numele cu semn, se înțelege tot ca `@tine`, nu ca o pomenire străină.

Și pentru asta fiecare primește două-trei vorbe scrise de mână, pentru când
modelul tace — `pareriDeSine`, lângă `pareri`.

**Verificare:** îi scrii barmanului „numele meu este Alex", apoi „ce crezi despre
@tine?" — răspunde despre el, nu despre altul, iar când te întorci la el peste
alte două discuții încă știe cum te cheamă. În listă, cât vorbești cu el, nu apar
nici „Barmanul", nici vreo replică cu asteriscuri.

---

## Faza 16 — Mai mulți odată, și cine se bagă în vorbă

Până acum, o vorbă avea un singur ascultător și o singură pomenire: vorbeai cu
barmanul, îl pomeneai pe draconian, iar draconianul nu afla niciodată că s-a
vorbit de el. Faza asta deschide masa: poți chema mai mulți odată, iar localul
capătă dreptul să se bage peste tine.

### 1. Mai mulți pomeniți, fiecare cu răspunsul lui

Într-o vorbă încap câte pomeniri vrei: *„ce ziceți de bere, @draconian și
@reptilian?"*. Răspunde întâi cel cu care vorbești — el rămâne omul tău —, apoi,
pe rând, fiecare dintre cei chemați pe nume. Nu vorbesc deodată: între două
replici trec vreo două secunde, cât să apuci să citești, iar cât mai are cineva
de zis, punctele stau în fereastră și nici tu nu scrii peste ei.

Cine e chemat răspunde din locul lui, nu din senin: modelul primește și cu cine
vorbeai, ca să se audă că s-a băgat în discuția altuia. **Fără Ollama** merge
aceeași socoteală ca la Faza 14, doar că din locul lui: chemat singur, vorbește
despre el (`pareriDeSine`); chemat lângă altcineva, despre acela (`pareri`).

Nu răspunde cine nu poate: clovnul ești tu, deci rămâne doar subiect de vorbă, iar
musafirii de la masa a treia doar cât stau acolo. Ce se spune se aude și în local,
deasupra fiecărui cap, ca orice vorbă.

Cel chemat **ține minte** că a fost tras în vorbă: în firul lui rămâne scris și
ce s-a spus, și cu cine vorbeai — te muți la el și nu se miră.

### 2. Se bagă cineva în vorbă

Vorbești cu draconianul și, o dată la vreo douăsprezece vorbe, se bagă altcineva
din local: aude de la locul lui și zice și el ceva, deși nu-l întreba nimeni.
Draconianul îl repede — *„Mârrr. Cine te-a întrebat, Barmanule?"* — și discuția
merge mai departe.

- **Rar.** `BAGAREA.sansa` e 0.08, și e obiect, nu constantă, ca să i-o poată fixa
  testele. Prea des ar deveni o regulă, nu o întâmplare.
- **Numai între patru ochi.** Dacă ai chemat tu pe cineva pe nume, e destulă
  gălăgie la masă: nu se mai bagă și alții peste ei.
- **Două replici, în ordine.** Întâi cel băgat, apoi repezeala celui cu care
  vorbeai — și repezeala vine peste ce tocmai s-a zis, nu peste vorba ta.

**Datele.** Fiecare cu fire primește `bagari` — cu ce se bagă neîntrebat — și
`repezeli`, cu `{cine}` în loc de nume, ca la `pareri`. Amândouă intră în `FIRI`
și se oglindesc în `personaje.json`.

**Rămâne pe dinafară:** cel care se bagă nu se întoarce spre masă și nu se apropie;
se bagă doar cu vorba. Cei chemați răspund la rând, nu unul peste altul, și nu-și
răspund între ei — vorba tot spre tine se întoarce.

**Verificare:** îi scrii barmanului „ce ziceți de bere, @draconian și
@reptilian?" — răspund toți trei, pe rând, nu deodată. Cu `BAGAREA.sansa` pus pe
1, orice vorbă aduce un băgat și o repezeală.

---

## Faza 17 — Vorbă pe înțeles

Fără Ollama, discuția nu semăna a discuție. Îi ziceai omului de aur „ce faci" și
îți întorcea „Mută el întâi", fiindcă răspunsul se lua la nimereală din rezerva
lui — patru vorbe care n-aveau nicio treabă cu ce întrebaseși. Ținea minte doar
numele tău, și nu întreba niciodată nimic. Trei lipsuri, luate odată.

### 1. Se citește ce ai scris

Vorba ta trece întâi printr-o citire. Nu se înțelege orice — n-avem cum, fără
model —, dar se recunosc lucrurile cu care se poartă o discuție într-un bar:
un **salut**, **ce faci**, **cine ești**, ceva despre **joc**, ceva despre
**băutură ori mâncare**, un **da** scurt, un **mulțumesc**, o **plecare**. Ce nu
se recunoaște, dar se termină cu semnul întrebării, e tot o întrebare: pentru ea
fiecare are un **nu știu** al lui. Abia ce nu e nici întrebare, nici temă, cade
în rezervă, ca înainte.

Ordinea temelor contează, fiindcă o vorbă poate cădea în două deodată: „salut, ce
faci?" e o întrebare, nu un salut, iar „cum merge partida?" e despre joc, nu
despre el. Cine spune mai mult se caută primul.

**Datele.** Fiecare cu fire primește `raspunsuri`, cu câte două vorbe la fiecare
temă — nouă teme, unsprezece oameni. Barmanul întrebat de băutură zice „Berea e
rece, dacă întrebi"; spălătorul, „Voi beți, eu spăl paharele".

### 2. Ține minte ce i-ai spus despre tine

Numele se reținea de la Faza 15. De-acum se rețin și **„sunt obosit"**, **„mi-e
sete"**, **„îmi place berea"**, „vin din Cluj", „am o problemă" — lucrurile pe
care un om le-ar ține minte de la tine.

Se țin minte **întoarse spre tine**: „sunt obosit" se ține minte ca *„ești
obosit"*. Așa pot fi spuse înapoi fără să sune strâmb — și de el, fără model, și
de model, care le primește în prompt („ți-a mai spus despre el că ești obosit").
Cel mai mult trei odată: un bar ține minte, nu ține dosar.

Când îi spui, se vede că a auzit: *„Va să zică ești obosit."* Iar mai târziu îți
poate aduce vorba înapoi: *„Ziceai că ți-e sete. Tot așa?"*.

### 3. Te întreabă și ei pe tine

O discuție în care doar tu întrebi nu e discuție. Din când în când întoarce și el
o întrebare — din ale lui (*„Tu ce bei?"*), despre altcineva din local (*„Pe
Draconianul îl știi?"*), sau despre ce i-ai spus mai devreme.

- **Nu la fiecare vorbă**: `INTREBATUL.sansa` e 0.35, și trebuie să fi trecut cel
  puțin două replici de la ultima întrebare. Altfel n-ar fi discuție, ci
  interogatoriu.
- **Niciodată peste o întrebare de-a lui**: dacă răspunsul se termina oricum cu
  semnul întrebării, nu se mai lipește una.
- **Numai cine e întrebat sau chemat pe nume.** Cine se bagă neîntrebat peste voi
  (Faza 16) n-are dreptul să și întrebe.

**Datele.** `intrebari`, două de fiecare, lângă `raspunsuri`.

**Rămâne pe dinafară:** citirea e după cuvinte, nu după înțeles — „nu mi-e sete"
se citește tot ca băutură. Ce ține minte se pierde la refresh, ca tot restul.
Cine vrea o discuție care înțelege cu adevărat pornește Ollama; astea sunt
schelăria de dedesubt, ca scena să nu tacă prostește fără el.

**Verificare:** îi scrii omului de aur „ce faci" și îți răspunde la asta, nu
altceva; îi spui „sunt obosit" și reține; peste câteva vorbe te întreabă el ceva.

---

## Faza 18 — Fără repetat, strigat pe nume, legat de dinainte

Trei lucruri văzute într-o discuție adevărată, după Faza 17. Nu teme noi, ci
locurile unde vorba tot suna a mașinărie.

### 1. Nu spune aceeași vorbă de două ori

Fiecare are două-trei replici la fiecare temă, deci întrebat de două ori același
lucru repeta imediat. De-acum **fiecare își ține minte ultimele patru vorbe pe
care le-a spus** și alege dintre celelalte. Când le-a spus pe toate, o ia de la
capăt — dar niciodată cu vorba de dinainte, fiindcă repetarea imediată se aude
cel mai tare. Regula ține pentru tot ce spune fără model: rezerva, temele,
părerile, băgările, repezelile, chiar și întrebările pe care ți le pune.

Asta e și memoria lui despre el însuși: nu doar ce i-ai spus tu, ci și ce a spus
el. (Cu modelul pornit, firul discuției îi ducea deja propriile replici.)

### 2. Strigat pe nume, numele stă la vocativ

„Cine te-a întrebat, **Barmanul**?" nu se zice în românește: se zice
**Barmanule**. Numele intră la vocativ oriunde e strigat cineva — adică în
`repezeli`, singurul loc unde `{cine}` e cel căruia i se vorbește, nu cel despre
care se vorbește.

Se schimbă doar primul cuvânt, cel care e numele. Ce vine după rămâne dacă e
parte din nume — *„Omule de aur"*, *„Spălătorule de vase"* — și cade dacă e doar
locul unde stă: *„Musafirul din stânga"* → *„Musafirule"*.

### 3. Ce spui acum se leagă de ce s-a spus înainte

Te întreba *„Pe Bucătarul îl știi?"*, îi ziceai *„cine este"*, și îți întorcea o
vorbă despre cărțile lui — fiindcă „cine este" nu era nicio temă, deci cădea în
rezervă. De-acum:

- **`cine e ăla?` e o temă.** Răspunsul e chiar vorba scrisă despre acela
  (`despre`), aceeași care ajunge în promptul modelului la o pomenire.
- **Numele merge scris fără `@`**: *„cine e barmanul?"* îl găsește după nume și
  porecle, ca la pomenire.
- **Iar dacă n-ai numit pe nimeni**, e vorba de cine te întrebase el:
  întrebarea lui rămâne deschisă exact până răspunzi, apoi se închide.

**Rămâne pe dinafară:** o singură întrebare ținută deschisă, ultima. Vocativul e
după terminația numelui, nu după gramatică adevărată — merge fiindcă toți cei
din local se numesc „-ul".

**Verificare:** îl întrebi de trei ori la rând „ce faci" și îți răspunde de
fiecare dată altfel; cel băgat în vorbă e repezit cu *„Chelnerule, taci"*; îi
scrii „cine e bucătarul?" și îți spune cine e bucătarul.

---

## Faza 19 — Când te iei de ei

Un bar nu e numai saluturi și „ce mai faci". Îi ziceai draconianului *„te crezi
mare șef sau ce?!"* și îți întorcea *„Cărțile astea sunt prea mici pentru mine"* —
fiindcă înțepătura nu era nicio temă, deci cădea în rezervă. Or, dintre toate
vorbele care i se pot spune unui draconian, asta e ultima la care ar trebui să
răspundă pe lângă.

**Înțepătura e o temă**, și una care trece peste toate celelalte: dacă te-ai luat
de el, nu-și mai spune părerea despre nimeni și nu mai răspunde la ce ai mai
scris în aceeași vorbă — îți întoarce vorba. Se recunosc *„te crezi..."*, *„cine
te crezi"*, *„sau ce?!"*, *„taci"*, *„nu știi să joci"*, *„te bat"*, *„sunt mai
bun ca tine" *și ocările obișnuite, cu tot cu terminații: „prostule", „fraiere",
„boule".

**Fiecare se supără cum îi e firea** (`raspunsuri.provocare`, câte trei de
fiecare): draconianul mârâie *„Îți iau capul, nu levata"*, reptilianul zâmbește
*„Vorbește cine a pierdut ultima mână"*, dealerul te avertizează *„Încă un cuvânt
și nu-ți mai împart"*, bucătarul îți amintește că are cuțit în mână, iar
musafirul tăcut zice *„Ho."*.

**Și nu rămâne la vorbă.** Cei de la masă se zbârlesc pe loc — o tresărire mai
iute decât la orice mână pierdută —, iar draconianului îi ia foc botul: mai mult
foc și mai lung decât când pierde, fiindcă o mână se pierde, dar o vorbă proastă
i se spune în față. Cei din local (barmanul, bucătarul, cei de la șah) n-au
tresărirea asta desenată, deci ei doar îți răspund cum se cuvine.

Cu Ollama pornit, modelul primește și el vestea: *„tocmai te-a luat peste picior,
în față — întoarce-i vorba tăios, fără să te scuzi"*.

**Verificare:** îi scrii draconianului „te crezi mare șef sau ce?!" și îți
răspunde la asta, cu foc pe bot; îi scrii „ce faci" și nu ia foc degeaba.

(Supărarea nu ținea, la Faza 19: îl înjurai, îți răspundea, și la vorba
următoare era iar cum era. Ține de la Faza 20.)

---

## Faza 20 — Furia ținută minte, și vorba dintre ei

Trei fire care se leagă unul de altul: supărarea rămâne, localul vorbește și
fără tine, iar supărarea trece de la unul la altul.

### 1. Furia se ține minte

Fiecare are acum o supărare, un număr între zero și unu. O înțepătură îl
enervează cu o jumătate — **două și e furios de tot** — și i se scurge singură în
vreo două minute. Nu se salvează nicăieri: la refresh, localul te iartă.

Furia schimbă **tot** ce spune, nu doar răspunsul la înjurătură: cu cât e mai
furios, cu atât mai des îți vorbește urât, orice l-ai întreba. Furios de tot, îi
zici „ce faci" și îți întoarce *„M-ai scos din sărite. Taci."*. De-aceea fiecare
primește `furii` — trei vorbe de om supărat, mai grele decât cele de la
`provocare`.

Se citește supărarea **de dinaintea vorbei tale**: prima înjurătură îl scoate din
sărite, dar primește răspunsul de om înțepat; a doua îl prinde deja furios. Fără
asta, prima ar sări direct la vorba cea mai grea și n-ar mai fi nicio treaptă.

### 2. Vorbesc între ei, singuri

Localul nu tace când taci tu. La 22–50 de secunde, doi dintre ei încep singuri o
vorbă: unul zice ceva, celălalt îi răspunde peste vreo două secunde. Se aude
doar deasupra capetelor, ca vorbele de la masa a treia — **în fereastra ta nu
intră**, fiindcă acolo e discuția ta, nu a lor.

Trucul care le face vorba coerentă fără să scriem dialoguri: cel care începe
întreabă ceva din `intrebari` — deja propoziții adevărate —, iar întrebarea lui
trece prin **aceeași citire de teme** ca vorbele tale, deci celălalt răspunde la
subiect. „Joci des?" → tema *joc* → *„Ăia joacă de-o oră, eu tot alerg."* Cu
Ollama pornit, răspunsul îl scrie modelul, care primește și el vestea că nu
vorbește cu un client, ci cu unul de-al casei.

Se dau la o parte cât ai tu pe cineva la răspuns: nu se vorbesc peste tine.

### 3. Furia se ia

Dacă cel care începe e furios, nu mai întreabă nimic — se răstește, din `furii`.
Iar **celălalt se aprinde și el**, cu o treime din supărarea lui, așa că îi
răspunde tot urât. Așa ajunge o înjurătură dată draconianului să se plimbe prin
tot localul, din vorbă în vorbă, și să se stingă de la sine.

**Rămâne pe dinafară:** furia e un singur număr, nu ține minte pe cine e supărat
— e supărat, atât, pe toată lumea deodată. Doi câte doi vorbesc, nu trei.

**Verificare:** îl înjuri de două ori pe draconian, apoi îl întrebi „ce faci" și
îți răspunde urât; îl lași în pace două minute și e iar cum era. Lăsând scena să
meargă, se aud vorbe între ei — iar dacă unul e furios, se ceartă.

---

## Faza 21 — Masa are un capăt

Puteai cere zece beri și douăsprezece farfurii una după alta. Chelnerul le aducea
pe toate, iar blatul se umplea de pahare puse unul într-altul, cu cinci sticle
înșirate lângă clovn. Se strâng, când se golesc — dar ceri mai repede decât apucă
el să ducă vasele la chiuvetă.

**Fiecare are trei locuri în față.** Se numără paharele și farfuriile din dreptul
lui, plus ce i se pregătește: ce e pe tavă acum, ce așteaptă la coadă și ce ai
bifat fără să fi trimis încă. Altfel ai cere de trei ori, cât n-a ajuns nimic pe
masă.

**Ce nu încape nu se poate bifa**, și se vede dinainte: rândul stă stins în
meniu, ca un fel terminat din bucătărie. Iar dacă tot apeși pe el, chelnerul îți
spune de ce — *„N-aveți unde le pune."* Un chelner care nu ia comanda și nu zice
nimic pare stricat, nu politicos.

**Sticla e a mesei, nu a unuia singur.** Aici era gaura cea mare: un rând de
cinste cu vin (Faza 11.4) punea patru sticle deodată, câte una de fiecare. De-acum
încap două pe blat, iar cine primește vin peste ele capătă doar paharul și își
toarnă din ce e acolo. Tot de-aceea `toarnaDinSticla` caută un pahar care nu e
plin — întâi al tău, fiindcă tu ai apăsat pe sticlă, apoi al celui care a
cerut-o. În paharul plin nu se mai toarnă: nu se varsă vinul degeaba.

**Și cinstea se uită la masă**: dacă n-au toți unde pune, rândul se lasă pe mâna
următoare.

**Rămâne pe dinafară:** locurile sunt numărate, nu măsurate — o farfurie de mici
ține cât un pahar de suc. Masa se golește singură, deci nu e nevoie să ștergi
nimic tu.

**Verificare:** ceri de la meniu tot ce scrie acolo, de zeci de ori la rând, și
în fața nimănui nu se strâng mai mult de trei lucruri, nici pe masă mai mult de
două sticle.

---

## Faza 22 — Intrarea în local

Scena se deschidea direct în mijlocul unei mâini: vedeai cărți zburând și nu
știai nici că poți comanda, nici că poți vorbi cu ei, nici ce bate ce. Faza asta
pune în față **clovnul**, o dată, la deschiderea paginii.

**Un panou pătrat**, în mijloc, cu localul stins în spate. În el, clovnul — de
data asta **cu fața la tine**: la masă îl vezi din spate, fiindcă stă între tine
și blat, dar când te salută se întoarce. Același costum, aceleași culori,
aceleași proporții, plus ce nu se vede niciodată în joc: ochi care clipesc rar,
obraji fardați, nasul roșu în mijloc și un zâmbet cât toată fața. E singurul loc
din scenă unde i se vede fața, de-aceea desenul stă aici, lângă panou, nu lângă
cel de la masă.

E și **mic**: cât o treime din înălțimea panoului. Vorba lui e lucrul important
aici, el e doar cel care o spune — prima încercare îl făcea cât tot panoul și
ajungea cu pălăria peste text.

Deasupra lui, ce spune acum, rupt în rânduri cât să încapă. Jos, în stânga, câte
buline câte vorbe are de spus, ca paginile unei povești.

**Șase vorbe**, în ordine: cine e, că ești tu; că îți zice ce se poate face; că un
clic pe cineva de la masă cheamă chelnerul; regulile cărților, cu jokerul care
bate zecele; că poți vorbi cu ei, dar se supără dacă ești rău; și că intră și el
la o levată. Fiecare stă pe ecran cât stai tu cu ea: nimic nu se grăbește singur,
vorba trece abia când apeși. După ultima, panoul se stinge și partida începe.

**Butoanele, în dreapta jos.** *„Înainte ›"* e mereu acolo; *„‹ Înapoi"* apare
abia când ai la ce te întoarce, adică de la a doua vorbă încolo. Ele sunt
singurele locuri din panou pe care se apasă: un clic în rest nu face nimic —
altfel ai sări peste o vorbă doar fiindcă ai dat un clic ca să pornească sunetul.
De pe ultima vorbă, „înainte" închide panoul.

Butoanele sunt singurul mers înainte. La început vorbele treceau și singure, după
un răgaz socotit din lungimea lor, dar răgazul e al cititorului, nu al nostru: unul
citește de două ori, altul dintr-o privire. Singurul lucru care mai merge de la
sine e stinsul de după ultima vorbă.

**Se arată o dată.** Nu e un meniu care se deschide cu o tastă: apare la
deschiderea paginii și nu se mai întoarce în sesiunea aia.

**Cât ține, cărțile așteaptă** — e singurul lucru din scenă care oprește partida,
ca să nu-ți scape prima mână cât citești. Localul din spate merge înainte, ca să
nu pară o poză.

**Rămâne pe dinafară:** la refresh se arată din nou, ca tot ce ține scena asta
minte. Ca să apară o singură dată vreodată ar trebui ceva salvat în browser, iar
regula de până acum e că nimic nu trece de un refresh.

**Verificare:** deschizi pagina, clovnul spune toate șase vorbele și panoul se
închide singur; nu se mai deschide cu nicio tastă, iar în tot timpul ăsta nu s-a
jucat nicio carte.

---

## `personaje.json` — toate personajele, într-un loc

Toți doisprezece cu nume sunt în fișier: cei trei de la masă, dealerul,
chelnerul, barmanul, bucătarul, spălătorul, cei doi de la șah și cei doi
musafiri.

**Ce e fișierul.** Oglinda scenei, nu sursa ei — o fișă de personaj citită de om,
cu firea, replicile de rezervă, locul în local și dimensiunile fiecăruia, ca să
se vadă toată distribuția pe o pagină, fără să umbli prin cele o sută de mii de
rânduri din `index.html`. Nu se citește la pornire și nu are voie să se citească:
regula „deschizi `index.html` și atât" înseamnă `file://`, unde `fetch` cade —
iar cu rezervă în cod datele ar ajunge să stea în două locuri, exact ce fișierul
ar trebui să dreagă. Scena rămâne singurul fișier care se execută.

**Cine intră.** Doar cine are nume în scenă. Clovnul intră deși n-are fire — pe
el îl joci tu, deci `fire` lipsește și scrie de ce.

**Ce scrie despre fiecare.** `fire`, `rezerva`, `despre`, `porecle`, `pareri`,
`pareriDeSine`, `bagari`, `repezeli`, `raspunsuri` și `intrebari`, copiate din `FIRI`; `scena` — locul la masă sau în local (în
`kx`/`ky`, ca de la Faza 12), lățimea, înălțimea, scara; pentru cei patru de la
masă și `gandire` cu `haos`, că se vede în cât stau pe gânduri.

**Când se atinge codul, se atinge și fișierul.** El e oglinda, `index.html` e
adevărul: dacă cele două se despart, fișierul e cel greșit.

**Verificare:** fiecare nume din `FIRI` are o intrare în `personaje.json` cu
aceeași fire și aceleași replici, iar dimensiunile din `scena` sunt cele din cod.

---

## Reguli pentru fazele 7 încolo

1. Fundalul nu are voie să fure atenția: mai mic, mai întunecat, mai puțin
   detaliat, fără culori la fel de tari ca la cei trei.
2. Fundalul și jocul sunt două bucle separate, care nu se așteaptă una pe alta.
   Fundalul nu primește niciun eveniment din partidă — dar de la Faza 8 are voie
   să *vadă* ce se întâmplă la masa noastră, prin `vizita.vede`, și să comenteze
   la următorul lui schimb de vorbe.
3. Personajele de fundal respectă același contract ca cei trei, ca să nu apară
   un al doilea fel de a face un personaj.
4. Chelnerul se mișcă pe un drum dat în unități U, ca tot restul, ca să se
   scaleze corect pe orice ecran.
5. Sunetul e un adaos, nu o condiție: cu boxele închise nu se pierde nimic din
   ce se vede.
6. Interacțiunile sunt trei, și rămân trei: cărțile tale (Faza 10), comanda
   (Faza 9) și vorba (Faza 13). Nu se adaugă a patra.

## Ce s-a hotărât la fazele 9–10

- **Meniul** e o listă scrisă — felul și prețul —, nu farfurii desenate
  lângă personaj: la nouă feluri, desenele s-ar fi bătut cap în cap.
- **Adversarii** sunt conduși de Ollama când e pornit și de o judecată scrisă în
  cod când nu e. Amândouă, nu una din două (vezi Faza 10).
- **Regula partidei** e cea mai mare carte ia levata, plus jokerul de la Faza 11.
  Fără atu; cartea întoarsă a dealerului a rămas de hotărât la Faza 12.

---

## Decizii tehnice

1. **Siluetele respectă același contract ca personajele reale.** Înlocuirea lor
   la Fazele 3-5 e o modificare locală, nu o rescriere.
2. **Tot ce ține de timp trece prin `dt`**, nu prin numărători de cadre. Scena
   arată la fel pe un ecran de 60 Hz și pe unul de 144 Hz.
3. **Personajele nu cunosc regulile.** Mașina de stări le trimite doar
   evenimente: `gandeste`, `joaca`, `castig` și `pierdere` (pe levată),
   `vecinCastiga` (cine a luat levata, singurul despre altcineva), iar după a
   treia levată `castigaMana` și `pierdeMana`.
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
de pe `file://` nu merg. E unul singur pentru tot folderul, în `server.py`, o
treaptă mai sus:

```
python ../server.py
```

apoi `http://localhost:8765/personaj/teste.html`. Mereu același port, oricâte
lucruri noi ar apărea alături, și fără nimic ținut minte de browser: un refresh
aduce fișierul de acum, nu pe cel de acum zece minute.

## Rămas de făcut

- **Faza 11, punctele 4 și 5:** cine pierde mâna face cinste, și ce-au băut și
  mâncat le schimbă firea. Sunt condimente, se pun oricând.
- **Faza 12, cartea întoarsă a dealerului:** de ales între „atu" și „pe dos".
  Până se alege, dealerul doar împarte.
- **Împărțitul cu 4 cărți din care arunci una,** dacă se dovedește că 3 e prea
  puțin de hotărât (vezi Faza 11).
