---
title: Azure-säkerhet och efterlevnad modell - FedRAMP Web Applications Automation - Underhåll
description: FedRAMP Web Applications Automation - Underhåll
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: de7dd5b4651f7f74d90d9d026af71cd676c720e6
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
ms.locfileid: "29150927"
---
# <a name="maintenance-ma"></a>Underhåll (MA)

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Läs NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

## <a name="nist-800-53-control-ma-1"></a>NIST 800 53 kontrollen MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>Princip för underhåll av system och procedurer

**MA-1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] Underhåll systemprinciper som åtgärdar syfte, scope, roller, ansvar, management åtagande, samordning bland organisationens entiteter och efterlevnad; och procedurer för att underlätta genomförandet av princip för underhåll av systemet och associerade system Underhåll kontroller. och granskar och uppdaterar den aktuella system Underhåll principen [tilldelning: organisation definierats frekvens]; och underhållsåtgärder system [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens systemprincipen Underhåll på företagsnivå och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-2a"></a>NIST 800 53 kontrollen MA-2.a

#### <a name="controlled-maintenance"></a>Kontrollerade Underhåll

**MA-2.a** organisationen schemalägger, utför, dokument och granskar poster för underhåll och reparation av information systemkomponenter i enlighet med tillverkare eller leverantören specifikationer och/eller organisatoriska krav.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för kontrollerad underhåll. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-2b"></a>NIST 800 53 kontrollen MA-2.b

#### <a name="controlled-maintenance"></a>Kontrollerade Underhåll

**MA-2.b** organisationen godkänner och övervakar alla underhållsaktiviteter om utförs på plats eller via fjärranslutning och huruvida utrustningen är underhålls på platsen eller bort till en annan plats.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för kontrollerad underhåll. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-2c"></a>NIST 800 53 kontrollen MA-2.c

#### <a name="controlled-maintenance"></a>Kontrollerade Underhåll

**MA-2.c** organisationen kräver att [tilldelning: personal som definierats av organisationen eller roller] godkänna explicit borttagning av informationssystem eller systemkomponenter från organisationens lokaler för extern underhåll eller reparationer.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure kräver att egenskapen tillgångar (t.ex. nätverksenhet eller server) kräver överföring externt har explicit tillgången ägarens godkännande. |


 ## <a name="nist-800-53-control-ma-2d"></a>NIST 800 53 kontrollen MA-2.d

#### <a name="controlled-maintenance"></a>Kontrollerade Underhåll

**MA-2.d** organisationen sanitizes utrustning för att ta bort all information från tillhörande media innan tas bort från organisationens resurser för extern underhåll eller reparationer.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure tillgången skydd Standard definierar tillgången hantering försiktighetsåtgärder som krävs för externt överföring av tillgångar. Tillgångsinformation skydd Standard kräver att lagring datatillgångar avmarkerad/rensas i överensstämmelse med NIST SP 800-88, riktlinjer för rensning av Media, innan de lämnar datacentret. |


 ## <a name="nist-800-53-control-ma-2e"></a>NIST 800 53 kontrollen MA-2.e

#### <a name="controlled-maintenance"></a>Kontrollerade Underhåll

**MA-2.e** Organisationen kontrollerar alla potentiellt påverkas säkerhetskontroller för att verifiera att kontrollerna som fortfarande fungerar korrekt efter underhåll eller reparationer åtgärder.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för kontrollerad underhåll. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-2f"></a>NIST 800 53 kontrollen MA-2.f

#### <a name="controlled-maintenance"></a>Kontrollerade Underhåll

**MA-2.f** organisationen innehåller [tilldelning: organisation definierats underhållsrelaterade information] i organisationens Underhåll poster.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för kontrollerad underhåll. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ma-2-2a"></a>NIST 800-53 Control MA-2 (2).a

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Kontrollerade Underhåll | Automatisk underhållsaktiviteter

**MA-2 (2) en** organisationen använder automatisk mekanismer för att schema, uppförande, och dokumentet underhåll och reparationer.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att automatisera underhållsaktiviteter. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ma-2-2b"></a>NIST 800-53 Control MA-2 (2).b

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Kontrollerade Underhåll | Automatisk underhållsaktiviteter

**MA-2 (2) .b** organisationen ger uppdaterad, korrekt, och alla poster för alla åtgärder för reparation och begärt schemalagda i processen och slutförs.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att automatisera underhållsaktiviteter. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-3"></a>NIST 800 53 kontrollen MA-3

#### <a name="maintenance-tools"></a>Verktyg för underhåll

**MA-3** organisationen godkänner kontroller, och övervakar informationsverktyg för Systemunderhåll.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure använder flera verktyg för att slutföra underhållet. Programvaruhantering verktyg godkänts, kontrolleras och underhålls med Microsoft Azure ändra och utgivningen. <br /> Platstjänster teamet upprätthåller en förteckning över godkända underhållsverktyg för användning i datacenter (se MA-3). Underhållspersonal dirigeras att använda de angivna underhållsverktyg. Datacenter management godkännande krävs för att kunna använda inte verktygen i datacentret. Fysiska handverktyg (skruvmejslar, wrenches osv.) är undantagna från den här kontrollen. <br /> Varje anläggning innehåller en begränsad fysiska lås eller åtkomst-kontrollerade utrymme för lagring av särskilda underhållsverktyg fluke ether-scope, fluke fiber channel Testare, Ethernet-sidor, t.ex. Platstjänster teamet utför rutinmässiga inventering kontroller för att kontrollera statusen för alla verktyg. Åtkomst till låsa rutan eller underhåll utrymme spåras i åtkomst Aktivitetsikon reader loggarna, vilket är tillgängligt i händelse av en undersökning. |


 ### <a name="nist-800-53-control-ma-3-1"></a>NIST 800 53 kontrollen MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Underhållsverktyg | Inspektera verktyg

**MA-3 (1)** organisationen kontrollerar underhållsverktyg befordras till en anläggning underhållspersonal för felaktig eller obehöriga ändringar.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure Site Services-teamet upprätthåller en förteckning över godkända underhållsverktyg för användning i datacenter (se MA-3 för ytterligare information). Underhållspersonal dirigeras att använda de angivna underhållsverktyg. DCM-godkännande krävs för att kunna använda inte verktygen i datacentret. |


 ### <a name="nist-800-53-control-ma-3-2"></a>NIST 800 53 kontrollen MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Underhållsverktyg | Inspektera Media

**MA-3.2** organisationen kontrollerar media som innehåller diagnostisk och testa program för skadlig kod innan media används i systemet.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure tillåter inte att använda mobildata eller lagringsmedia i produktionsmiljö av Microsoft Azure-datacenter utan godkännande för hantering av datacenter. Användning av personligt ägda media är förbjuden används i produktionsmiljön för Microsoft Azure-datacenter. <br /> Microsoft Azure har implementerat en process för att inspektera bärbara datorer innan som används i produktionsmiljön för Microsoft Azure-datacenter. Säkerhet polis tränas anropar personal som använder bärbara datorer i produktionsmiljön för att kontrollera att de bärbara datorerna har genomgått och genomgått. |


 ### <a name="nist-800-53-control-ma-3-3"></a>NIST 800 53 kontrollen MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Underhållsverktyg | Förhindra att obehöriga borttagning

**MA-3.3** organisationen förhindrar obehörig borttagning av underhållsutrustning som innehåller organisationsinformation genom att verifiera att det finns ingen organisationsinformation finns på utrustningen; rensa eller förstöra den utrustning. behålla utrustning i funktionen; eller skaffa ett undantag från [tilldelning: personal som definierats av organisationen eller roller] auktorisera explicit borttagning av utrustning från funktionen.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure använder datacenter specifika underhållsverktyg som finns kvar i funktionen och tas inte bort. Varje anläggning innehåller ett begränsat fysiska lås rutan eller lagring rum som lagrar underhållsverktyg fluke ether-scope, fluke fiber channel Testare, Ethernet-sidor, t.ex. Åtkomst styrs till Lås rutan eller lagring rum i DCAT förhindra obehörig åtkomst till verktyg för Systemunderhåll. <br /> Organisationsinformation skyddas under underhåll av kontrollerna i MA-4. För att komma åt organisationsinformation har användaren Privilegierade konton och autentiserare. |


 ## <a name="nist-800-53-control-ma-4a"></a>NIST 800-53 Control MA-4.a

#### <a name="nonlocal-maintenance"></a>Icke-lokala Underhåll

**MA-4.a** organisationen godkänner och övervakar icke-lokala underhåll och diagnostik aktiviteter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utföra icke-lokala Underhåll på kunden distribuerade operativsystem. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-4b"></a>NIST 800 53 kontrollen MA-4.b

#### <a name="nonlocal-maintenance"></a>Icke-lokala Underhåll

**MA-4.b** organisationen tillåter användning av icke-lokala underhåll och diagnostik verktyg som överensstämmer med organisationens policy och dokumenteras i skyddsplan för systemet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utföra icke-lokala Underhåll på kunden distribuerade operativsystem. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-4c"></a>NIST 800 53 kontrollen MA-4.c

#### <a name="nonlocal-maintenance"></a>Icke-lokala Underhåll

**MA-4.c** organisationen använder starka autentiserare vid upprättandet av icke-lokala underhåll och diagnostik sessioner.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utföra icke-lokala Underhåll på kunden distribuerade operativsystem. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-4d"></a>NIST 800 53 kontrollen MA-4.d

#### <a name="nonlocal-maintenance"></a>Icke-lokala Underhåll

**MA-4.d** organisationen underhåller poster för icke-lokala underhåll och diagnostik aktiviteter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utföra icke-lokala Underhåll på kunden distribuerade operativsystem. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-4e"></a>NIST 800 53 kontrollen MA-4.e

#### <a name="nonlocal-maintenance"></a>Icke-lokala Underhåll

**MA-4.e** Organisationen avslutas sessionen och Nätverksanslutningar när icke-lokala Underhåll har slutförts.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utföra icke-lokala Underhåll på kunden distribuerade operativsystem. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ma-4-2"></a>NIST 800-53 Control MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Icke-lokala Underhåll | Dokumentet icke-lokala Underhåll

**MA-4.2** organisation dokument i skyddsplan för systemet, principer och procedurer för etablering och användning av icke-lokala underhåll och diagnostik anslutningar.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att dokumentera icke-lokala Underhåll i skyddsplan för kunden distribuerade operativsystem. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ma-4-3"></a>NIST 800-53 Control MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Icke-lokala Underhåll | Jämförbara säkerhet / rensning

**MA-4.3** organisationen kräver att icke-lokala underhåll och diagnostik tjänster utföras från ett informationssystem som implementerar en säkerhet funktion jämförbar kapaciteten införts på datorn genomgår underhåll, eller tar bort komponenten servas från informationssystem innan icke-lokala underhåll eller diagnostiska services sanitizes komponenten (avseende organisationsinformation) före borttagning från organisationens verksamhet och när tjänsten är utföra, kontrolleras och sanitizes komponenten (avseende eventuellt skadlig programvara) innan du återansluter komponenten till systemet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utföra alla icke-lokala underhåll av kunden distribuerade operativsystem från ett informationssystem som har jämförbara säkerhet. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ma-4-6"></a>NIST 800-53 Control MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Icke-lokala Underhåll | Kryptografiska skydd

**MA-4.6** informationssystem implementerar kryptografiska metoder för att skydda integritet och sekretess för icke-lokala underhåll och diagnostik kommunikation.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att implementera kryptografiska metoder när du utför icke-lokala underhåll och diagnostik av kunden distribuerade operativsystem. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-5a"></a>NIST 800 53 kontrollen MA-5.a

#### <a name="maintenance-personnel"></a>Underhållspersonal

**MA-5.a** organisationen upprättar en process för underhåll personal auktorisering och upprätthåller en lista över behöriga Underhåll organisationer eller personal.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå Underhåll personal auktoriserings- och eskort systemprocesser kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-5b"></a>NIST 800 53 kontrollen MA-5.b

#### <a name="maintenance-personnel"></a>Underhållspersonal

**MA-5.b** organisationen garanterar att icke-åtföljda personal som utför underhåll på systemet har nödvändiga åtkomst tillstånd.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå Underhåll personal auktoriserings- och eskort systemprocesser kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-5c"></a>NIST 800 53 kontrollen MA-5.c

#### <a name="maintenance-personnel"></a>Underhållspersonal

**MA-5.c** organisationen anger organisationens personal med nödvändiga åtkomsten tillstånd och tekniska behörighet att övervaka underhållsaktiviteter för personal som inte har tillstånd som krävs.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå Underhåll personal auktoriserings- och eskort systemprocesser kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ma-5-1a"></a>NIST 800 53 kontrollen MA-5 (1) en

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Underhållspersonal | Enskilda användare utan behörighet

**MA-5 (1) en** organisationen implementerar procedurer för användning av underhållspersonal som saknar rätt säkerhetsbehörigheter fillås eller som inte USA medborgarna som innehåller de följande kraven underhållspersonal som inte har nödvändig tillstånd för åtkomst, fillås eller formella åtkomst godkännanden åtföljda och övervakas under prestanda för underhåll och diagnostik aktiviteter för systemets information av godkända organisationens personal som fullständigt avmarkerad har lämplig tillstånd för åtkomst och är tekniskt kvalificerade; innan initierande underhåll eller diagnostiska aktiviteter personal som inte behöva åtkomst tillstånd, fillås eller formella åtkomst godkännanden, alla temporär information lagringskomponenter i systemet är språkoberoende och alla nonvolatile lagringsmedia tas bort eller fysiskt kopplas från systemet och skyddas.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå Underhåll personal auktoriserings- och eskort systemprocesser kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ma-5-1b"></a>NIST 800 53 kontrollen MA-5 (1) .b

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Underhållspersonal | Enskilda användare utan behörighet

**MA-5 (1) .b** organisation utvecklar och implementerar alternativa säkerhetsåtgärder i event komponenten information system inte kan vara språkoberoende, tas bort eller kopplas från systemet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå Underhåll personal auktoriserings- och eskort systemprocesser kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ma-6"></a>NIST 800 53 kontrollen MA-6

#### <a name="timely-maintenance"></a>Rimlig Underhåll

**MA-6** organisationen hämtar Underhåll support och/eller extra delar för [tilldelning: organisation definierats information systemkomponenter] i [tilldelning: definierad tidsperiod organisation] för felet.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure-Datacenter Underhåll resident underhållspersonal för att stödja kritiska datacenter infrastruktursystem samt datacenter åtgärder. Team har identifierat kritiska systemkomponenter för säkerhet och teknik som de bibehåller besparar för på-plats. Kritiska system som är utformade i N + 1 konfigurationer och tjänster som är utformade för att hantera. Detta gör att datacenter management-teamet att uppfylla Återställningsmål om ett avbrott i tjänsten eller beredskapsplan situation. Viktig information systemtjänster etableras från flera datacenter för att undvika avbrott i tjänsten på grund av en incident på någon av datacenter. Kundprogram ansvarar för att distribuera till flera Datacenter för att ge redundans och återhämtning. |
