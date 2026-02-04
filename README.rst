==========================
HTTP-forbindelseslivscyklus
=========================

.. indhold:: Indholdsfortegnelse

Motivation
==========

Ved tech-jobsamtalen eller på andre måder er et ofte stillet spørgsmål, hvad der sker, når du skriver en URL i browseren? Hvad sker der bag kulisserne, når du surfer på et websted? Hvad indebærer en typisk HTTP-forbindelseslivscyklus? Jeg vil besvare disse spørgsmål efter bedste overbevisning.

OSI-modellen
=============

Før vi dykker ned i forbindelsesprocessen, lad os gennemgå den grundlæggende OSI-model (Open Systems Interconnection-model). OSI-modellen er en konceptuel model, der standardiserer kommunikationen mellem to systemer - et, hvor anmodningen stammer fra (klienten), og et, der betjener anmodningen og sender et svar tilbage (serveren). Tabellen nedenfor viser nogle af de vigtige egenskaber ved hvert lag.

+--+--------------+------------+-----------------------------------+--------------------+
|Nej|Lag | Hardware | Funktion | Protokoller/Apps | Tilføjelser |
+==+===============+==============+==============================================+======================+
|7 |Applikation | Server/PC | Applikationer, brugergrænseflade | HTTP, SMTP, DNS | L7 Header |
+--+--------------+-------------+------------------------------------+---------------------+
|6 | Præsentation | Server/PC | Håndterer kryptering og syntaksændringer| JPEG, MP3 | L6 Header |
+--+--------------+------------+-----------------------------------+---------------------+
|5 | Session | Server/PC | Godkendelse, tilladelser, sessioner| SCP, OS-planlægning | L5 Header |
+--+--------------+------------+--------------+---------------------------------+--------------------+
|4 |Transport | Firewall | End-to-end-levering, fejlkontrol | TCP, UDP | L4 Header |
+--+--------------+-------------+--------------+---------------------------------+
|3 |Netværk | Routere | Netværksadressering, routing, switching | IP | L3 Header |
+--+--------------+------------+----------------------------------+---------------------+
|2 |Datalink | Switche | Fysisk adresse, fejldetektion, flow | Ethernet, frame relay | L2 Header/Trailer|
+--+--------------+------------+-------------+-----------------------------------+----------------------+
|1 |Fysisk | Kabler | Bit overført over fysisk netværk| EIA/TIA | L1 Header |
+--+--------------+------------+-----------------------------------+---------------------+

Forbindelsen
==============

Trin 1: Analyse af URL'en
-------------------------

Så snart du skriver URL'en i browseren og trykker på Enter/Return-tasten, vil browseren (eller enhver klient for den sags skyld) analysere URL'en [#url]_ for at udtrække vigtige komponenter fra den. Et eksempel på en URL er givet nedenfor:

**https://www.google.com/search?q=cats**

Her søger vi bare efter katte på Google. Fra ovenstående URL er ``https://`` protokollen, ``google.com`` er værten på ``www`` (internettet), ``/search`` er stiparameteren, og ``?q=cats`` er en forespørgselsstrengparameter, der angiver, at vi forespørger Google efter katte [#komponenter_af_url]_.

Trin 2: DNS (adresseopløsning)
----------------------------------

Da browseren nu har kendskab til den vært, den forsøger at nå, i dette tilfælde ``google.com``, vil den forsøge at få dens tilsvarende IP-adresser. Domæner som '*.com' eller '*.org' blev oprettet, så vi nemt kan huske dem. Men for at browseren kan sende den faktiske anmodning til f.eks. google.com, skal den bruge værtens IP-adresse. DNS-opløsning hjælper os med at få IP-adresseoplysningerne for et givet domænenavn. DNS findes på applikationslaget (L7) fra ovenstående tabel.

Trin i DNS-opløsning:

* Browseren kontrollerer først sin lokale browsercache for at se, om domænenavnet til IP-adressen allerede findes. Hver post i DNS har en TTL-værdi, og posterne opbevares kun i cachen indtil deres TTL-udløbstid, hvorefter den skal hente posten igen.

* Hvis posten ikke findes i browsercachen, kontrollerer DNS-resolveren operativsystemets cache. Operativsystemet opbevarer sin egen DNS-opløsningscache for websteder, der for nylig er blevet besøgt af forskellige klienter (f.eks. en browser) på den pågældende maskine. På din lokale computer kan DNS-cachen kontrolleres ved hjælp af kommandoen ``$ ipconfig /displaydns`` i Windows eller ``$ log stream --predicate 'process == "mDNSResponder"' --info`` på Mac/Linux.

* Hvis posten ikke findes i operativsystemets cache, kontrolleres det, om DNS-posten findes i den cache, der vedligeholdes af internetudbyderen (ISP).

* Hvis posten ikke findes, selv i ISP-cachen, foretages en forespørgsel til DNS-serveren via internettet. I dette tilfælde siden køen
