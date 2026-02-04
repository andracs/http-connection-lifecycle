# HTTP Connection Lifecycle – Murderbot Style 😒🖤

## Motivation 😑

Okay, så du vil vide, hvad der sker, når du trykker "Enter" efter at have skrevet en URL? Du vil have mig til at forklare det, som om du ikke bare kigger på kattevideoer igen... Fint. Here’s the deal – vi går igennem det trin for trin, så også du kan føle dig som en tech-guru. Prøv nu ikke at falde i søvn. 🙄

---

## OSI-modellen: 🥱 Et overblik, ingen bad om  

Forestil dig, at internettet er en lagkage. Ikke en velsmagende en – nej, en teknisk. OSI-modellen har syv lag, og hver især har en vigtig rolle. Eller noget...

| Lag | Hvad det gør                     | Eksempler           |
|-----|----------------------------------|---------------------|
| 7   | **Applikation** 🌐             | Din browser (HTTP)  |
| 6   | **Præsentation** 🎨             | Kryptering (TLS)    |
| 5   | **Session** 🧑‍🤝‍🧑             | Holder samtaler kørende |
| 4   | **Transport** 🚛                | Sikrer data når frem |
| 3   | **Netværk** 🌍                  | Finder vej (IP)     |
| 2   | **Data Link** 🔗                | MAC-adresser, bla bla |
| 1   | **Fysisk** ⚡                   | Kabler og strøm     |

Så ja, lagkagen ser fancy ud, men det smager af rugbrød og pligt. Skal vi videre? 🙃

---

## HTTP Connection Lifecycle: Din tur ned ad bits-and-bytes lane 🛣️

Ah, livscyklussen for en HTTP-forbindelse! Det lyder som en filosofisk aktivitet, men det er egentlig bare computere, der prøver ikke at lave ged i den.

---

### 1. Parsing af URL 🌐
Du skriver "https://www.google.com/search?q=kittens" i din browser... Selvfølgelig gør du det. Brok-boksen (din browser) splitter tingene:
- **https** → Vi gør det “sikkert”, ellers brokker Google sig. 👮
- **google.com** → Hvor vi prøver at gå hen. Suk.
- **/search?q=kittens** → Katte? Igen? Seriøst...

---

### 2. DNS: “Google siger du? Aldrig hørt om det.” 🤔
Din computer aner ikke, hvad "google.com" er. Jaja, det lyder smart, men den har brug for en IP. 

Den prøver dette i rækkefølge:
1. Browsercache – Nope.
2. OS-cache – Nope. 
3. ISP-cache – Nææh, hvorfor skulle vi gøre livet nemmere?
4. DNS-servere – Åh, godt! Lad os spørge internettets telefonbog! 🔍  

Efter en længere tur når den frem til: **172.217.164.174**. Endelig en adresse. Videre. 

---

### 3. TCP/IP-pakke: Post for dummies 📦
Tænk på det som et brev, fyldt med data. Adresserne (MAC og IP) er hvor det skal hen. Men hey, tingene skiftes hele tiden ud. Håber ikke noget falder af. 😐

---

### 4. Gateway: "Fortæl mig MAC-adressen, ellers…" 🕵️‍♀️
Inden vi kan sende vores små bitte datapakker ud i verden, skal vi banke på døren til vores lokale router: "Hvem er du, og hvad er din Mac... adresse?" ARP står klar til at finde svaret.

---

### 5. Velkommen til Router-ruten 🎢
Got adresse? Fedt. Nu hopper vi mellem routere, mens de spiller "Find pixelerede katte-videoer". Miss en enkelt, og dit request? Ja, det forsvinder bare i glemslen. 🤷

---

### 6. Three-way Handshake: Skal vi være venner? 🤝
Inden serveren vil give os noget som helst, skal klienten (dig) og serveren gennemføre en akavet "Hej, er vi ok nu?"-session:
1. Klienten: "Hej, her er mit navn. Lad os tale!"
2. Serveren: "Ok, fint, hvad vil du?"
3. Klienten: "Nu hvor vi er enige, hvor er mine katte?!"  

Nåh, det er klaret? Videre.

---

### 7. TLS: Er dette sikkert, eller hvad?! 🛡️
Hvis siden bruger HTTPS, spørger browseren:
- **Browser**: "Er du virkelig Google?" 
- **Serveren**: "Ja da! Her er et certifikat! Tro mig!" 
- **Browseren**: "Hmm... Nå okay." 🕵️

Nu er alt krypteret. Ingen må høre om din besættelse af katte. 

---

### 8. Serveren reagerer 🍽️
Du venter, mens serveren roder gennem sine millioner af forespørgsler: "Åh ja, kattenøglen 'q-kittens', den har vi lige her!". Den svarer med din data. Endelig.

---

### 9. Farvel-session 👋
Nu kan forbindelsen lukkes, fordi serveren gider ikke bruge tid på dig mere. Hvis du har brug for noget andet? Start forfra. Suk.

---

## Konklusion 🔮

Så dér har du det. Hver gang du søger på "kittens", tager din browser, din computer og hele internettet en kollektiv dyb indånding og laver et forsøg på at forstå dig. Vi er alle trætte.

👽 *Held og lykke med dine næste kattevideoer!*
