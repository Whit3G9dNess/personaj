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

  **Paharele nu apar din senin.** Se așază la masă goală și abia când trece
  chelnerul pe la ei — are un popas al lui acolo — le apare ceva de băut.

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

Aici scena capătă singurul ei buton, și primul sunet. Vine după tot restul
fiindcă are nevoie de el: de chelner ca să aducă, de bar ca să toarne, de masă
ca să pună paharul pe ea.

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
răspund cu ce carte joacă. `personaje.json` are deja pregătit, pentru fiecare,
un `promptSistem` și o `temperature` — de acolo își iau firea.

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

**Stadiu: 1, 2 și 3 sunt gata.** Ce s-a mai văzut pe drum, scris aici ca să nu
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
  celui cu cele mai multe și celui cu cele mai puține. De aici se poate lega,
  mai încolo, și punctul **4**: cine pierde mâna face cinste.

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
4. **Cine răspunde.** Modelul din Ollama, fiecare cu firea lui: cei trei o au
   deja în `personaje.json`, ceilalți primesc câte un `promptSistem` scurt, tot
   acolo. Răspunsul apare în fereastră și, scurtat, și deasupra capului lui în
   scenă, ca vorbele de la masa a treia.
5. **Fără Ollama.** Regula de aur rămâne: dacă modelul lipsește sau tace, fiecare
   are câteva răspunsuri scrise de mână, potrivite cu firea lui. Nu se blochează
   nimic și nu apare niciun mesaj de eroare — pur și simplu răspund mai scurt.

**Partida merge înainte** cât ții fereastra deschisă, ca la meniu: cărțile rămân
ale tale, iar dacă îți vine rândul și nu joci, joacă el după douăsprezece
secunde.

**Rămâne pe dinafară:** vorbă între ei doi pe subiectul tău, memorie de la o
vizită la alta, comenzi date prin chat („adu-mi o bere") — comanda rămâne la
meniu.

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

## `personaje.json` — toate personajele, într-un loc

Fișierul a rămas la cele trei de la masă, deși în local sunt doisprezece cu nume
și cu fire: cei trei, dealerul, chelnerul, barmanul, bucătarul, spălătorul, cei
doi de la șah și cei doi musafiri. Se completează cu toți, în aceeași formă.

**Ce e fișierul.** Oglinda scenei, nu sursa ei — o fișă de personaj citită de om,
cu firea, replicile de rezervă, locul în local și dimensiunile fiecăruia, ca să
se vadă toată distribuția pe o pagină, fără să umbli prin cele o sută de mii de
rânduri din `index.html`. Nu se citește la pornire și nu are voie să se citească:
regula „deschizi `index.html` și atât" înseamnă `file://`, unde `fetch` cade —
iar cu rezervă în cod datele ar ajunge să stea în două locuri, exact ce fișierul
ar trebui să dreagă. Scena rămâne singurul fișier care se execută.

**Cine intră.** Doar cine are nume în scenă. Clovnul intră deși n-are fire — pe
el îl joci tu, deci `fire` lipsește și scrie de ce.

**Ce scrie despre fiecare.** `fire` și `rezerva`, copiate din `FIRI`; `scena` —
locul la masă sau în local, lățimea, înălțimea, scara; pentru cei patru de la
masă și `gandire` cu `haos`, că se vede în cât stau pe gânduri.

**De resincronizat.** Cele trei intrări vechi s-au depărtat de cod (reptilianul
e 13×25 în scenă, nu 10.5×21) și țin locul în unghiuri, deși scena așază după
`kx`/`ky` de la Faza 12. Se iau valorile din `index.html`, ele sunt adevărul.

**Verificare:** fiecare nume din `FIRI` are o intrare în `personaje.json` cu
aceeași fire și aceleași replici, iar dimensiunile din `scena` sunt cele din cod.

---

## Reguli pentru fazele 7–10

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

## Ce rămâne de hotărât la fazele 9–10

- Cum arată meniul de comandă: farfurii și pahare desenate lângă personaj, sau
  un rând de cuvinte jos.
- Dacă adversarii de la Faza 10 sunt conduși de Ollama sau de o judecată scrisă
  în cod (vezi Faza 10).
- Ce reguli are partida adevărată: cea mai mare carte ia mâna, sau ceva cu atu.

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
de pe `file://` nu merg. E unul singur pentru tot folderul, în `server.py`, o
treaptă mai sus:

```
python ../server.py
```

apoi `http://localhost:8765/personaj/teste.html`. Mereu același port, oricâte
lucruri noi ar apărea alături, și fără nimic ținut minte de browser: un refresh
aduce fișierul de acum, nu pe cel de acum zece minute.

## Rămas de făcut

- A rămas o singură siluetă cu numele scris deasupra, reptilianul. Numele e un
  ajutor temporar de dezvoltare și dispare la Faza 5.
- Brațul dinspre privitor al clovnului e acoperit de cărțile din mână. Se vede
  când personajul le ridică; de rezolvat, dacă deranjează, la Faza 6.
