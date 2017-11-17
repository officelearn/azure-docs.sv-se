---
title: Azure betalning bearbetning modell - krav
description: PCI DSS krav 12
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 05e9ed7c886d37a024db1eedbc541705b7d8a9a9
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Krav för PCI DSS-kompatibel miljöer  
## <a name="pci-dss-requirement-12"></a>PCI DSS krav 12

**Underhålla en princip som åtgärdar informationssäkerhet för all personal**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

En stark säkerhetsprincip anger tonen säkerhet för hela entiteten och informerar personal vad som förväntas av dem. All personal bör känna till hur känsliga data och deras ansvarsområden för att skydda den. Vid tillämpningen av krav 12 refererar ”personal” till heltid och deltid anställda, temporärt anställda, leverantörer och konsulter som är ”fast” på sin webbplats eller annars har åtkomst till data-miljön Kortinnehavarens.

## <a name="pci-dss-requirement-121"></a>PCI DSS krav 12.1

**12.1** upprätta, publicera, underhålla och sprida en säkerhetsprincip.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att etablera och hantera en säkerhetsprincip för information.|



### <a name="pci-dss-requirement-1211"></a>PCI DSS krav 12.1.1

**12.1.1** granska säkerhetsprincipen minst årligen och uppdatera principen när miljön ändras.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att uppdatera deras information säkerhetsprincip minst årligen eller ändringar i deras Kortinnehavarens datamiljö (CDE).|



## <a name="pci-dss-requirement-122"></a>PCI DSS krav 12,2

**12,2** implementera en riskbedömning bearbeta som:
- Utförs minst en gång om året och efter betydande ändringar i miljön (till exempel förvärv, fusion, flytt, etc.)
- Identifierar kritiska tillgångar, hot och säkerhetsrisker
- Resulterar i en formell, dokumenterat analys för risk.
- > Exempel på riskbedömning metoder inkludera, men är inte begränsade till OKTAV och ISO 27005 NIST SP 800 – 30.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att implementera en risk assessment process som tar alla hot som anges i kravet 12,2.|



## <a name="pci-dss-requirement-123"></a>PCI DSS krav 12.3

**12.3** utveckla användningsprinciper för viktiga tekniker och definiera korrekt användning av dessa tekniker.

> [!NOTE]
> Exempel på viktiga tekniker inkludera, men är inte begränsade till fjärråtkomst och trådlös teknik, bärbara datorer, surfplattor, elektroniska flyttbar, e-post och Internetanvändning.
Se till att dessa användningsprinciper kräver följande.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1231"></a>PCI DSS krav 12.3.1

**12.3.1** explicit godkännande av auktoriserade parterna

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1232"></a>PCI DSS krav 12.3.2

**12.3.2** autentisering för användning av tekniken

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1233"></a>PCI DSS krav 12.3.3

**12.3.3** en lista över alla dessa enheter och personal med åtkomst

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1234"></a>PCI DSS krav 12.3.4

**12.3.4** en metod för att korrekt och lätt fastställa ägare, kontaktinformation och syfte (exempelvis etiketter, kodning och/eller inventeringen av enheter)

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1235"></a>PCI DSS krav 12.3.5

**12.3.5** Acceptable använder teknik

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1236"></a>PCI DSS krav 12.3.6

**12.3.6** godkända nätverksplatser för

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att fastställa godkända nätverksplatser för molnbaserade virtuella datorer, lagring och stödande tjänster.|



### <a name="pci-dss-requirement-1237"></a>PCI DSS krav 12.3.7

**12.3.7** lista över företaget har godkänt produkter

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att fastställa godkända nätverksplatser för molnbaserade virtuella datorer, lagring och stödande tjänster.|



### <a name="pci-dss-requirement-1238"></a>PCI DSS krav 12.3.8

**12.3.8** automatisk frånkoppling av sessioner för fjärråtkomst tekniker efter en viss period av inaktivitet

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure använder Microsoft företagets AD session Lås funktioner, vilket tillämpar session lås-timeout efter en tids inaktivitet. Nätverksanslutningar avslutas efter 30 minuter av inaktivitet. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1239"></a>PCI DSS krav 12.3.9

**12.3.9** aktivering av fjärråtkomst tekniker för leverantörer och affärspartner bara vid behov genom att leverantörer och affärspartner med omedelbar inaktiveringen efter användning

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-12310"></a>PCI DSS krav 12.3.10

**12.3.10** för personal som har åtkomst till kreditkortsinformation via fjärråtkomst tekniker, förhindra på Kopiera, flytta och lagring av kreditkortsinformation till lokala hårddiskar och flyttbara medier som elektroniska, såvida inte explicit behörighet för en definierad företagets behov.
Om det finns en auktoriserad företagets behov, kräver användningsprinciper data skyddas i enlighet med alla tillämpliga PCI DSS krav.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att säkerställa att personal som har åtkomst till kreditkortsinformation via fjärråtkomst teknik är förbjudna att kopiera, flytta och lagra kreditkortsinformation på lokala hårddiskar och flyttbara medier som elektroniska, såvida inte explicit behörighet för en definierad affärsbehov.|



## <a name="pci-dss-requirement-124"></a>PCI DSS krav 12.4

**12.4** Kontrollera säkerhetsprinciper och procedurer tydligt definiera information ansvarsområden för all personal.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1241"></a>PCI DSS krav 12.4.1

**12.4.1** ytterligare krav för endast tjänstleverantörer: verkställande management fastställa ansvar för skydd av kreditkortsinformation och ett PCI DSS efterlevnad program att inkludera:
- Övergripande ansvar för att underhålla PCI DSS kompatibilitet
- Definiera en auktoriserad för ett PCI DSS efterlevnad program och kommunikation till företagsledningen 

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder som är leverantörer ansvarar för att dokumentera deras kompatibilitet PCI-programmet.|



## <a name="pci-dss-requirement-125"></a>PCI DSS krav 12,5

**12,5** tilldela till en person eller ett team följande information security management ansvarsområden.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att definiera och tilldela information ansvarsområden till sina anställda.|



### <a name="pci-dss-requirement-1251"></a>PCI DSS krav 12.5.1

**12.5.1** upprätta, dokumentera och distribuera säkerhetsprinciper och rutiner.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att definiera och tilldela information ansvarsområden till sina anställda.|



### <a name="pci-dss-requirement-1252"></a>PCI DSS krav 12.5.2

**12.5.2** övervaka och analysera säkerhetsaviseringar och information och distribuera till vederbörlig personal.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att definiera och tilldela information ansvarsområden till sina anställda.|



### <a name="pci-dss-requirement-1253"></a>PCI DSS krav 12.5.3

**12.5.3** upprätta, dokumentera och distribuera säkerhet incident svar och Eskalering procedurer för att säkerställa rätt tid och effektiv hantering av alla situationer.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1254"></a>PCI DSS krav 12.5.4

**12.5.4** administrera användarkonton, inklusive tillägg, borttagningar och ändringar.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



### <a name="pci-dss-requirement-1255"></a>PCI DSS krav 12.5.5

**12.5.5** övervaka och kontrollera alla tillgång till data.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer dikterar rätt användning, implementering och autentisering för viktiga tekniker i deras CDE.|



## <a name="pci-dss-requirement-126"></a>PCI DSS krav 12,6

**12,6** implementera en formell säkerhetsprogrammet medvetenhet om att all personal som är medvetna om vikten av Kortinnehavarens data säkerhetsprinciper och procedurer.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa och hantera principer omgivande medvetenheten för personal med åtkomst till CDE.|



### <a name="pci-dss-requirement-1261"></a>PCI DSS krav 12.6.1

**12.6.1** utbilda personal vid hyra och minst en gång om året. 

> [!NOTE]
> Metoder kan variera beroende på rollen personalens och deras åtkomstnivå till Kortinnehavarens data.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för personal ta emot och bekräfta informationssäkerhet och PCI-DSS medvetenhet utbildning minst en gång om året.|



### <a name="pci-dss-requirement-1262"></a>PCI DSS krav 12.6.2

**12.6.2** behöver personal minst årligen bekräftar att de har läst och förstått säkerhetsprinciper och procedurer.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för personal ta emot och bekräfta informationssäkerhet och PCI-DSS medvetenhet utbildning minst en gång om året.|



## <a name="pci-dss-requirement-127"></a>PCI DSS krav 12,7

**12,7** skärmen potentiella personal före anlitar för att minska risken för attacker från interna källor. (Exempel på bakgrundskontroller är tidigare anställningshistorik, straffrättsliga post, kredithistorik och referens kontrollerar.) 

> [!NOTE]
> De potentiella personal som kan vara anlitat för vissa placerar i till exempel store kassörerna som bara har tillgång till ett nummer i taget när underlätta en transaktion, det här kravet är endast en rekommendation.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för personal med åtkomst till CDE kontrolleras grundlig bakgrund.|



## <a name="pci-dss-requirement-128"></a>PCI DSS krav 12,8

**12,8** underhåll och implementera principer och procedurer för att hantera leverantörer som kreditkortsinformation delas eller som kan påverka säkerheten för kreditkortsinformation, enligt följande.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att övervaka efterlevnaden av PCI för tjänstleverantörer som kreditkortsinformation delas eller kan påverka säkerheten för CDE. Kunderna måste upprätthålla en lista över alla ger används i sina CDE.|



### <a name="pci-dss-requirement-1281"></a>PCI DSS krav 12.8.1

**12.8.1** underhålla en lista över leverantörer, inklusive en beskrivning av den tjänst som tillhandahålls.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att övervaka efterlevnaden av PCI för tjänstleverantörer som kreditkortsinformation delas eller kan påverka säkerheten för CDE. Kunderna måste upprätthålla en lista över alla ger används i sina CDE.|



### <a name="pci-dss-requirement-1282"></a>PCI DSS krav 12.8.2

**12.8.2** upprätthålla ett skriftligt avtal som innehåller en bekräftelse-leverantörer ansvarar för att skydda kreditkortsinformation tjänstproviders ha eller på annat sätt att lagra, hantera och överföra för kunden, eller i den utsträckning som de kan påverka säkerheten för kundens Kortinnehavarens datamiljö. 

> [!NOTE]
> Den exakta texten i en bekräftelse beror på avtal mellan de två parterna, information om tjänsten tillhandahålls och ansvarsområden som tilldelats varje part. Bekräftelsen behöver inte inkludera den exakta texten i det här kravet.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att underhålla skriftligt avtal med leverantörer bekräftades ansvar för att upprätthålla säkerheten för kreditkortsinformation.|



### <a name="pci-dss-requirement-1283"></a>PCI DSS krav 12.8.3

**12.8.3** se till att det finns en upprättad process för att tjänsteleverantörer, inklusive korrekt på grund av fordringar före engagement.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att säkerställa att det finns en upprättad process för att tjänsteleverantörer, inklusive korrekt på grund av fordringar före engagement.|



### <a name="pci-dss-requirement-1284"></a>PCI DSS krav 12.8.4

**12.8.4** upprätthålla ett program för att övervaka-leverantörer PCI DSS kompatibilitetsstatus minst en gång om året.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att underhålla ett program för att övervaka-leverantörer PCI DSS kompatibilitetsstatus minst en gång om året.|



### <a name="pci-dss-requirement-1285"></a>PCI DSS krav 12.8.5

**12.8.5** underhålla information om vilka PCI DSS krav som hanteras av varje tjänstprovider och som hanteras av entiteten.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att behålla en kopia av den [ansvar sammanfattning matrisen](https://aka.ms/pciblueprintcrm32), vilket beskrivs PCI DSS kraven som är ansvar för kunden och de som har ansvar för Microsoft Azure.|



## <a name="pci-dss-requirement-129"></a>PCI DSS krav 12.9

**12.9** ytterligare krav för endast tjänstleverantörer: leverantörer bekräftar vid skrivning till kunder som är ansvarig för att skydda kreditkortsinformation tjänstleverantör har eller på annat sätt lagrar processer, eller Överför för räkning av kunden eller i utsträckning som de kan påverka säkerheten för kundens Kortinnehavarens datamiljö. 

> [!NOTE]
> Den exakta texten i en bekräftelse beror på avtal mellan de två parterna, information om tjänsten tillhandahålls och ansvarsområden som tilldelats varje part. Bekräftelsen behöver inte inkludera den exakta texten i det här kravet.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder som är leverantörer ansvarar för bekräftades deras ansvarsområden för underhåll av PCI-kompatibilitet. |



## <a name="pci-dss-requirement-1210"></a>PCI DSS krav 12.10

**12.10** implementera en plan för incidentsvar. Var beredd på att svara omedelbart på säkerhetsöverträdelser system.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att utveckla IR planer och tester som tar hänsyn till kunden kontroller som är relaterade till delade touch punkter och kundprogram utnyttja Azures infrastruktur. Det är kundens ansvar att ge exakta kontaktinformation till Azure om en incident måste rapporteras till dem som kan påverka deras program eller data.|



### <a name="pci-dss-requirement-12101"></a>PCI DSS krav 12.10.1

**12.10.1** skapa incidentsvarsplanen implementeras om systemet. Se till att planen adresser följande minst:
- Roller och ansvarsområden strategier för kommunikation och kontakta vid röjande inklusive meddelanden om betalning varumärken, minst
- Procedurer för specifika incidenter
- Företag procedurer för kontinuitet och återställning
- Processer för säkerhetskopiering av data
- Analys av juridiska krav för rapportering kompromisser
- Omfattningen av och svar för alla kritiska systemkomponenter
- Referens eller inkludering av incidenter procedurer från märken för betalning

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att utveckla IR planer och tester som tar hänsyn till kunden kontroller som är relaterade till delade touch punkter och kundprogram utnyttja Azures infrastruktur. Det är kundens ansvar att ge exakta kontaktinformation till Azure om en incident måste rapporteras till dem som kan påverka deras program eller data.|



### <a name="pci-dss-requirement-12102"></a>PCI DSS krav 12.10.2

**12.10.2** granska och testa planen, inklusive alla element som anges i krav 12.10.1, minst en gång om året.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att utveckla IR planer och tester som tar hänsyn till kunden kontroller som är relaterade till delade touch punkter och kundprogram utnyttja Azures infrastruktur. Det är kundens ansvar att ge exakta kontaktinformation till Azure om en incident måste rapporteras till dem som kan påverka deras program eller data.|



### <a name="pci-dss-requirement-12103"></a>PCI DSS krav 12.10.3

**12.10.3** utse specifika personal som ska vara tillgängliga på grundval av 24/7 svarar på aviseringar.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att utveckla IR planer och tester som tar hänsyn till kunden kontroller som är relaterade till delade touch punkter och kundprogram utnyttja Azures infrastruktur. Det är kundens ansvar att ge exakta kontaktinformation till Azure om en incident måste rapporteras till dem som kan påverka deras program eller data.|



### <a name="pci-dss-requirement-12104"></a>PCI DSS krav 12.10.4

**12.10.4** ge lämplig utbildning personal säkerhetsintrång svar ansvarsområden.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att utveckla IR planer och tester som tar hänsyn till kunden kontroller som är relaterade till delade touch punkter och kundprogram utnyttja Azures infrastruktur. Det är kundens ansvar att ge exakta kontaktinformation till Azure om en incident måste rapporteras till dem som kan påverka deras program eller data.|



### <a name="pci-dss-requirement-12105"></a>PCI DSS krav 12.10.5

**12.10.5** är aviseringar från säkerhetsövervakning system, inklusive men inte begränsat till identifiering av adressintrång, intrångsskydd, brandväggar och -integritet övervakningssystem.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att utveckla IR planer och tester som tar hänsyn till kunden kontroller som är relaterade till delade touch punkter och kundprogram utnyttja Azures infrastruktur. Det är kundens ansvar att ge exakta kontaktinformation till Azure om en incident måste rapporteras till dem som kan påverka deras program eller data.|



### <a name="pci-dss-requirement-12106"></a>PCI DSS krav 12.10.6

**12.10.6** utveckla en process för att ändra och utvecklas incidentsvarsplanen enligt erfarenheter och införa branschen utvecklingen.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att utveckla IR planer och tester som tar hänsyn till kunden kontroller som är relaterade till delade touch punkter och kundprogram utnyttja Azures infrastruktur. Det är kundens ansvar att ge exakta kontaktinformation till Azure om en incident måste rapporteras till dem som kan påverka deras program eller data.|



## <a name="pci-dss-requirement-1211"></a>PCI DSS krav 12.11

**12.11** **ytterligare krav för endast tjänstleverantörer:** utföra granskningar anställda att bekräfta personal följer säkerhetsprinciper och operativa procedurer.
Granskningar måste omfatta följande processer:
- Dagliga loggen granskar
- Brandväggen regeluppsättning granskning
- Använder konfigurationen till nya system
- Åtgärda säkerhetsaviseringar
- Procedurer för ändringshantering 

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder som är leverantörer ansvarar för att dokumentera granskningarna processer för att bekräfta PCI efterlevnad styr prestanda.|



### <a name="pci-dss-requirement-12111"></a>PCI DSS krav 12.11.1

**12.11.1** ytterligare krav för endast tjänstleverantörer: upprätthålla dokumentationen för varje kvartal granskningsprocessen för att inkludera:
- Dokumentera resultat granskningarna
- Granska och signering av resultat av personal tilldelats ansvar för PCI DSS efterlevnad programmet 

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder som är leverantörer ansvarar för att dokumentera granskningarna processer för att bekräfta PCI efterlevnad styr prestanda.|




