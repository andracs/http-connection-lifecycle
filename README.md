# HTTP-forbindelseslivscyklus
## Eller: Hvad fanden sker der egentlig, når du trykker Enter?

### Motivation

"Hvad sker der, når du skriver en URL i browseren?" - det spørgsmål der får tech-interviewere til at føle sig rigtig smarte. Men fair nok, det er faktisk ret interessant! Lad os dykke ned i, hvad der foregår bag kulisserne, når du desperat skal tjekke kattebilleder på nettet.

### OSI-modellen
*Fordi du ikke kan være rigtig nørd uden at kende dine 7 lag*

OSI-modellen (Open Systems Interconnection) er IT-verdenens svar på en lagkageopskrift - 7 lag af kommunikation mellem klient og server. Her er den korte version:

| # | Lag | Hardware | Funktion | Protokoller |
|---|-----|----------|----------|-------------|
| 7 | Applikation | Server/PC | Her bor dine apps og brugergrænseflader | HTTP, SMTP, DNS |
| 6 | Præsentation | Server/PC | Kryptering og dataformatering | JPEG, MP3 |
| 5 | Session | Server/PC | Godkendelse og sessionhåndtering | SCP |
| 4 | Transport | Firewall | End-to-end levering og fejlkontrol | TCP, UDP |
| 3 | Netværk | Routere | IP-adressering og routing | IP |
| 2 | Datalink | Switche | Fysiske adresser (MAC) | Ethernet |
| 1 | Fysisk | Kabler | De faktiske bits på tråden | EIA/TIA |

### Forbindelsen

#### Trin 1: URL-parsing
*Browseren læser din hjerne... øh, URL*

Du skriver `https://www.google.com/search?q=cats` og trykker Enter. Browseren dissekerer nu URL'en som en IT-forensiker:

- **Protokol**: `https://` (fordi vi er sikkerhedsbevidste)
- **Vært**: `google.com` på `www` 
- **Sti**: `/search`
- **Query**: `?q=cats` (fordi internet blev opfundet til katte)

#### Trin 2: DNS-opslag
*Telefonbogen fra helvede*

Browseren ved nu at du vil til `google.com`, men den skal bruge en IP-adresse. Domænenavne eksisterer kun fordi mennesker er dårlige til at huske tal som `142.250.74.78`. DNS (Domain Name System) oversætter menneskevenlige navne til maskinvenlige tal.

**DNS-opløsnings-flow:**

1. **Browser cache**: "Har jeg været her før for nylig?" - Hver DNS-post har en TTL (Time To Live), så browseren gemmer dem et stykke tid
   
2. **OS cache**: Hvis browseren ikke har det, tjekker operativsystemet sin egen cache. Tjek med `ipconfig /displaydns` (Windows) eller `log stream --predicate 'process == "mDNSResponder"' --info` (Mac/Linux)

3. **ISP cache**: Stadig intet? Din internetudbyder har sin egen DNS-cache
   
4. **DNS-server**: Sidste udvej - en rekursiv DNS-forespørgsel starter:
   - **Root server** → "Øh, prøv .com-serverne"
   - **TLD server** (.com) → "Google? Prøv deres navneservere"
   - **Authoritative server** → "Her er IP'en: 142.250.74.78"

Boom! Vi har en IP-adresse. Videre!

#### Trin 3: TCP Three-Way Handshake
*Det mest awkward håndtryk nogensinde*

Nu skal browseren etablere en TCP-forbindelse med serveren. TCP er det pålidelige transportlag (i modsætning til UDP, der er "yolo-mode"). Det kræver et three-way handshake - som at spørge om lov til at snakke:

1. **SYN**: Klient → Server ("Hej, må jeg snakke med dig?")
2. **SYN-ACK**: Server → Klient ("Ja selvfølgelig! Og må jeg svare?")
3. **ACK**: Klient → Server ("Yes! Lad os gøre det!")

Forbindelsen er etableret! Men vent... det er HTTP**S** - vi er ikke færdige endnu.

#### Trin 4: TLS/SSL Handshake
*Fordi Google ikke skal vide hvilke katte vi googler*

HTTPS betyder HTTP over TLS (Transport Layer Security). Før vi sender data, skal vi kryptere lortet:

1. **Client Hello**: "Hey, jeg snakker TLS 1.3, her er mine cipher suites"
2. **Server Hello**: "Cool, lad os bruge TLS 1.3 med AES-256-GCM"
3. **Certifikat**: Serveren sender sit SSL-certifikat (beviser identitet)
4. **Nøgleudveksling**: Asymmetrisk kryptering til at udveksle en symmetrisk nøgle
5. **Finished**: Begge parter bekræfter - kryptering aktiveret! 🔒

#### Trin 5: HTTP Request
*Endelig! Vi kan spørge efter kattene!*

Nu sender browseren en HTTP GET request:

```http
GET /search?q=cats HTTP/1.1
Host: www.google.com
User-Agent: Mozilla/5.0 (tror serveren jeg er Firefox)
Accept: text/html,application/json
Accept-Language: da-DK,da;q=0.9
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Cookie: [en masse tracking cookies]
```

#### Trin 6: Server-processering
*Magien sker*

Google's server modtager requesten og:

1. **Parser** requesten
2. **Router** den til den rigtige handler
3. **Kører** søgealgoritmer (hemmeligt black magic)
4. **Henter** resultater fra databaser/caches
5. **Genererer** HTML-respons med kattebilleder
6. **Komprimerer** med gzip (fordi båndbredde)

#### Trin 7: HTTP Response
*Serveren svarer tilbage*

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Encoding: gzip
Content-Length: 48293
Cache-Control: private, max-age=0
Set-Cookie: [endnu flere tracking cookies]

<!DOCTYPE html>
<html>
  <head><title>cats - Google Search</title></head>
  <body>
    <!-- 48KB med katte-goodness -->
  </body>
</html>
```

#### Trin 8: Browser Rendering
*Det visuelle klimaks*

Browseren modtager HTML'en og går i gang:

1. **Parser HTML** → bygger DOM-træet
2. **Parser CSS** → bygger CSSOM-træet
3. **Kombinerer** DOM + CSSOM → Render Tree
4. **Layout**: Beregner hvor alt skal være (reflow)
5. **Paint**: Tegner pixels på skærmen
6. **Compositing**: Samler lag sammen

Mens dette sker:
- **JavaScript** downloades og eksekveres (og ødelægger sandsynligvis noget)
- **Billeder** loades asynkront
- **Tredjepartsscripts** tracker dig (naturligvis)

#### Trin 9: Forbindelsen Lukkes... Eller Gør Den?

Med HTTP/1.1's `Connection: keep-alive` holdes TCP-forbindelsen åben, så du kan klikke på næste kattebillede uden at skulle igennem hele cirkusset igen.

Med HTTP/2 og HTTP/3 bliver det endnu vildere med multiplexing, server push og QUIC-protokollen - men det er en helt anden historie.

---

## Konklusion

Så næste gang nogen spørger "hvad sker der når du skriver en URL i browseren?", kan du konfident svare: "En sindsyg mængde kompleksitet, der på mirakuløs vis fungerer på millisekunder."

Og det hele... for kattebilleder. 🐱

---

### Ressourcer

- [MDN Web Docs - HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [How DNS Works](https://howdns.works/)
- [TCP/IP Illustrated](https://en.wikipedia.org/wiki/TCP/IP_Illustrated)
- [High Performance Browser Networking](https://hpbn.co/)

### Licens

MIT - fordi deling er caring (og jeg gider ikke advokater)
