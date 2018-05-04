---
title: "Azure-säkerhet och efterlevnad modell - FedRAMP Web Applications Automation - fysisk och miljösäkerhet skydd"
description: "FedRAMP Web Applications Automation - fysisk och miljösäkerhet skydd"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 792b9da0f4e5ec73c39f56a6e4805cf3c37133c4
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="physical-and-environmental-protection-pe"></a>Fysisk och miljösäkerhet skydd (PE)

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Läs NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

## <a name="nist-800-53-control-pe-1"></a>NIST 800 53 kontrollen PE-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Fysisk och miljösäkerhet Protection-principen och procedurer

**PE-1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] en fysisk och miljösäkerhet skyddsprincip som åtgärdar syfte, scope, roller, ansvar, management åtagande samordning av organisationens entiteter och efterlevnad; och procedurer för att underlätta genomförandet av fysisk och miljösäkerhet protection-principen och associerade fysisk och miljösäkerhet skydd kontroller. och granskar och uppdaterar den aktuella fysiska och miljösäkerhet skyddsprincipen [tilldelning: organisation definierats frekvens]; och procedurer som fysisk och miljösäkerhet skydd [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå fysisk och miljösäkerhet protection-principen och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-pe-2a"></a>NIST 800 53 kontrollen PE-2.a

#### <a name="physical-access-authorizations"></a>Fysisk åtkomst tillstånd

**PE-2.a** organisationen utvecklar, godkänner och upprätthåller en lista över personer med behöriga tillgång till funktionen där systemet finns.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Fysisk åtkomst till ett Microsoft-datacenter måste godkännas av Datacenter Management (DCM)-teamet med hjälp av verktyget datacenter åtkomst. Access-tilldelningar kräver ett slutdatum efter vilken åtkomst tas bort automatiskt och måste godkännas. Dessutom när åtkomst krävs inte längre, datacenter säkerhet polis eller hantering för att begära manuellt avslutning av åtkomst. |


 ## <a name="nist-800-53-control-pe-2b"></a>NIST 800 53 kontrollen PE-2.b

#### <a name="physical-access-authorizations"></a>Fysisk åtkomst tillstånd

**PE-2.b** organisationen utfärdar autentiseringsuppgifter för lokal åtkomst.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Verktyget datacenter åtkomst är den auktoritära källan som visar en lista över all personal med auktoriserad åtkomst till en specifik datacenter. Verktyget är kopplad till datacentrets fysiska access control säkerhetsenheter och auktoriserar åtkomst baserat på åtkomstnivåer som godkänns av DCM-teamet. Åtkomstnivåer som tilldelas i verktyget antingen en användares Microsoft utfärdat skylt eller en tillfällig åtkomst Aktivitetsikon som tilldelas på datacentret genom kontroll rum handledarens (CR). Åtkomstnivåer godkänns av DCM-teamet. Förutom autentiseringsuppgifterna som tilldelats fysiska Aktivitetsikoner kräver vissa områden i datacentret registrering av användarens biometriska data (Handgeometri eller fingeravtryck). |


 ## <a name="nist-800-53-control-pe-2c"></a>NIST 800 53 kontrollen PE-2.c

#### <a name="physical-access-authorizations"></a>Fysisk åtkomst tillstånd

**PE-2.c** organisationen går igenom listan över behöriga och åtkomst för enskilda användare [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Förutom åtkomstbegränsning som beskrivs i del en Microsoft Azure granskningar behörighet listor för Azure-Datacenter efter 90 dagar för att ta bort/uppdatera enskilda åtkomst efter behov. |


 ## <a name="nist-800-53-control-pe-2d"></a>NIST 800 53 kontrollen PE-2.d

#### <a name="physical-access-authorizations"></a>Fysisk åtkomst tillstånd

**PE-2.d** organisationen tar bort personer från listan och åtkomst när åtkomst krävs inte längre.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure tar bort åtkomst automatiskt när åtkomst Tilldelningens slutdatum har uppnåtts. När det krävs inte längre åtkomst, begär datacenter säkerhet polis eller hantering manuellt avslutning av access i verktyget datacenter åtkomst. Dessutom tar Microsoft Azure bort alla onödiga åtkomst tillstånd identifieras på grund av åtkomst listan Granska som beskrivs i del c. |


 ## <a name="nist-800-53-control-pe-3a"></a>NIST 800 53 kontrollen PE-3.a

#### <a name="physical-access-control"></a>Fysiska åtkomstkontroll

**PE-3.a** organisationen tillämpar fysisk åtkomst tillstånd vid [tilldelning: in-organisation definierats/utgång pekar på funktionen där systemet finns] genom att kontrollera enskilda åtkomst tillstånd innan du beviljar åtkomst till funktionen; och styra ingång-/ utgång till och med [markeringen (en eller flera): [tilldelning: organisation definierats fysisk åtkomst-datorer/enheter]; skydd].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure tillämpar fysisk åtkomst tillstånd för alla fysiska åtkomstpunkter till Azure-datacenter med 24 x 7 bemanning, larm, video övervakning, flerfunktionsautentisering och fälla enheter. |


 ## <a name="nist-800-53-control-pe-3b"></a>NIST 800 53 kontrollen PE-3.b

#### <a name="physical-access-control"></a>Fysiska åtkomstkontroll

**PE-3.b** organisationen underhåller fysisk åtkomst granskningsloggar för [tilldelning: in-organisation definierats/utgång punkter].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Alla har åtkomst till Azure-datacenter anläggningar loggas och granskas. |


 ## <a name="nist-800-53-control-pe-3c"></a>NIST 800 53 kontrollen PE-3.c

#### <a name="physical-access-control"></a>Fysiska åtkomstkontroll

**PE-3.c** organisationen ger [tilldelning: organisation definierats säkerhetsåtgärder] styra åtkomsten till områden inom funktionen officiellt utses offentligt tillgänglig.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Azure-Datacenter innehåller inte områden som är avsedda som offentligt tillgänglig. |


 ## <a name="nist-800-53-control-pe-3d"></a>NIST 800 53 kontrollen PE-3.d

#### <a name="physical-access-control"></a>Fysiska åtkomstkontroll

**PE-3.d** escorts besökare och övervakar besökare aktivitet [tilldelning: organisation definierats omständigheter kräver besökare escorts och övervakning].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Alla användare som har godkänt åtkomst till datacenter (se PE-2) har angetts som ”endast eskort' på deras Aktivitetsikoner eller via andra tydligt (t.ex. färgade Aktivitetsikoner) och krävs för att vara med deras escorts vid alla tidpunkter. Åtföljda besökare har inte någon åtkomstnivåer som beviljas åtkomst till dem och kan endast överföras i åtkomsten för sina escorts. Escorts övervaka alla aktiviteter med deras besökare i datacentret. |


 ## <a name="nist-800-53-control-pe-3e"></a>NIST 800 53 kontrollen PE-3.e

#### <a name="physical-access-control"></a>Fysiska åtkomstkontroll

**PE-3.e** Organisationen skyddar nycklar, kombinationer och andra enheter som fysisk åtkomst.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Fysiska nycklar och tillfällig åtkomst Aktivitetsikoner skyddas inom operations Säkerhetscenter (SOC). Säkerhet polis är personal 24 x 7. Nycklar har checkats ut till specifika personal genom att matcha personens åtkomst Aktivitetsikon till fysiska nyckel. Viktiga inventeringar utförs vid varje SKIFT och nycklar kan inte flyttas till annan plats. |


 ## <a name="nist-800-53-control-pe-3f"></a>NIST 800 53 kontrollen PE-3.f

#### <a name="physical-access-control"></a>Fysiska åtkomstkontroll

**PE-3.f** organisation inventeringar [tilldelning: organisation definierats fysisk åtkomst enheter] var [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Fysisk åtkomst enheter inom Azure-Datacenter inventerade årsvis minst. Nycklar och tillfällig åtkomst Aktivitetsikoner inventeras flera gånger per dag under varje SKIFT. Åtkomst Aktivitetsikon läsare och liknande enheter för åtkomst är länkade till den fysisk säkerhetssystem där status representeras kontinuerligt. |


 ## <a name="nist-800-53-control-pe-3g"></a>NIST 800 53 kontrollen PE-3.g

#### <a name="physical-access-control"></a>Fysiska åtkomstkontroll

**PE-3.g** organisationen ändras kombinationer och nycklar [tilldelning: organisation definierats frekvens] och/eller när nycklar tappas kombinationer skadas eller personer överförs eller avslutas.  

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure-Datacenter har procedurer för att implementera i fall när en åtkomst-skylt eller nyckel går förlorad eller en person avslutas eller överföras. Om ett avslutning eller överföring personens åtkomst bort omedelbart från systemet och deras åtkomst Aktivitetsikon tas bort. |


 ### <a name="nist-800-53-control-pe-3-1"></a>NIST 800 53 kontrollen PE-3 (1)

#### <a name="physical-access-control--information-system-access"></a>Fysiska åtkomstkontroll | Åtkomsten till information System

**PE-3 (1)** organisationen tillämpar tillstånd fysisk åtkomst till systemets information förutom fysiska åtkomstkontroller för den på [tilldelning: organisation definierats fysiska utrymmen som innehåller en eller flera komponenter av informationssystem].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure ytterligare begränsar områden i Microsoft-datacenter som innehåller kritiska system (t.ex. colocations, kritiska miljöer, MDF rum, etc.) via olika säkerhetsmekanismer som elektroniska åtkomstkontroll biometriska enheter, och skydd mot passback kontroller. Åtkomst till Azure colocations beviljas som en separat, högre åtkomstnivå DCAT än andra åtkomst delar av datacentret. Dessutom krävs alla Azure FTE och leverantörer som har åtkomst till Azure Government-colocations att formellt genomgå säkerhetskontroll för Microsofts molntjänster och USA medborgarskap verifiering innan den auktoriserade att komma åt miljön. Se PS-03-avsnittet för ytterligare information om kontrollen moln för Azure Government-colocations. |


 ## <a name="nist-800-53-control-pe-4"></a>NIST 800 53 kontrollen PE-4

#### <a name="access-control-for-transmission-medium"></a>Åtkomstkontroll för medier för överföring

**PE 4** organisation fysisk åtkomst till [tilldelning: organisation definierats information system distribution och överföring rader] inom organisationens verksamhet med [tilldelning: organisation definierats säkerhetsåtgärder] .

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure har implementerat åtkomstkontroll för medier för överföring via design- och skapande av Main Distribution ram (MDF) lokaler och colocations för att skydda information system distribution och överföring rader från oförutsedda skador avbrott och obehöriga. Åtkomst till MDF lokaler och colocations kräver två tvåfaktorsautentisering (åtkomst Aktivitetsikon och biometrik). Detta säkerställer att åtkomsten är begränsad till endast auktoriserad personal (se PE-2, PE 3). Inom MDF skyddas överföring och distribution rader från oförutsedda skador, avbrott och fysiska angrepp genom att använda metal rörledningar låst rack, burar eller kabelfack. |


 ## <a name="nist-800-53-control-pe-5"></a>NIST 800 53 kontrollen PE-5

#### <a name="access-control-for-output-devices"></a>Åtkomstkontroll för utdataenheter

**PE 5** organisation fysisk åtkomst till information system utdataenheter att förhindra att obehöriga får tillgång till utdata.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure-Datacenter har inte utdataenheter (bildskärmar, skrivare, ljudenheter, etc.) permanent anslutning till Azure tillgångar eller Azure delade resurser. Förutom att inte behöva utdataenheter kan utföra security polis fysiska genomgångar av funktionen flera gånger per SKIFT söker efter objekt som dörrar låsas och rack som skyddas. Datacenter åtkomst är begränsad till personer som har godkänt åtkomst tillstånd. Colocations kräver två tvåfaktorsautentisering (åtkomst Aktivitetsikon och biometrik) för att få åtkomst. |


 ## <a name="nist-800-53-control-pe-6a"></a>NIST 800 53 kontrollen PE-6.a

#### <a name="monitoring-physical-access"></a>Övervaka fysisk åtkomst

**PE-6.a** organisationen övervakar fysisk tillgång till funktionen där systemet som finns att upptäcka och fysiska oftare.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Fysisk åtkomst övervakas genom att implementera säkerhetsenheter och processer i datacenter. Exempel är 24 x 7 elektroniska övervakning av åtkomstkontroll, larm och video system samt 24 x 7 på platsen skyddspatrullering på anläggningen och skäl. En kontroll rum ansvarig finns i SOC alltid att tillhandahålla övervakning av fysisk åtkomst i datacentret. |


 ## <a name="nist-800-53-control-pe-6b"></a>NIST 800 53 kontrollen PE-6 b

#### <a name="monitoring-physical-access"></a>Övervaka fysisk åtkomst

**PE-6 b** organisationen granskar tillgångsloggar [tilldelning: organisation definierats frekvens] och på förekomsten av [tilldelning: händelser som definierats av organisationen eller potentiella tecken på händelser].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Tillgångsloggar har granskat kontinuerligt och underhålls för senare undersökning granskning. |


 ## <a name="nist-800-53-control-pe-6c"></a>NIST 800 53 kontrollen PE-6

#### <a name="monitoring-physical-access"></a>Övervaka fysisk åtkomst

**PE-6** organisationen samordnar resultaten av granskningar och undersökningar med organisationens incidenter-kapacitet. 

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Säkerhetshändelser som sker inom datacentret dokumenteras security-teamet. Säkerhetsteam skapar rapporter som samlar in information om säkerhetshändelse när händelsen inträffar. <br /> För incidenter som kräver government meddelanden, kommer att samordna säkerhetsteam för Microsoft Azure med providern större program (t.ex. O365) att meddela government agency kund, oss CERT och FedRAMP inom USA CERT riktlinjer (Se IR-6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>NIST 800 53 kontrollen PE-6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Övervaka fysisk åtkomst | Intrång larm / övervakningsutrustning

**PE-6.1** organisationen övervakar fysiska intrång larm och övervakningsutrustning.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Förutom 24 x 7 på plats säkerhet, också använda larm övervakning system och CCTV Microsoft Azure-datacenter (hyrd och fullständigt hanterade). Larm övervakas och besvarades av kontrollen rum handledarens sig 24 x 7 i SOC. Under en svar-situation använder kontroll rum handledarens kameror i området för incident som undersöks för att ge svarande realtidsinformation. |


 ### <a name="nist-800-53-control-pe-6-4"></a>NIST 800 53 kontrollen PE-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Övervaka fysisk åtkomst | Övervaka fysisk åtkomst till Information Systems

**PE-6.4** organisationen övervakar fysisk åtkomst till systemets information förutom fysisk åtkomst övervakning av funktionen som [tilldelning: organisation definierats fysiska utrymmen som innehåller en eller flera komponenter av informationen system].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure övervakar fysisk åtkomst till funktionerna samt informationssystem inom datacenter. Alla Microsoft online services-utrustning placeras i platser i datacenter där fysisk åtkomst är övervakad. Alla samordning och MDF rum skyddas av åtkomstkontroll, larm och video. |


 ## <a name="nist-800-53-control-pe-8a"></a>NIST 800 53 kontrollen PE-8.a

#### <a name="visitor-access-records"></a>Besökare åtkomstregister

**PE-8.a** organisationen underhåller besökare åtkomstregister för funktionen där systemet som finns för [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Datacenter åtkomstregister underhålls i verktyget datacenter åtkomst i form av godkända begäranden. Enligt beskrivningen i PE 3 måste besökare åtföljas vid alla tidpunkter. Eskort åtkomst inom datacentret loggas och om nödvändigt kan korreleras till besökscentret för framtida granskning. |


 ## <a name="nist-800-53-control-pe-8b"></a>NIST 800 53 kontrollen PE-8.b

#### <a name="visitor-access-records"></a>Besökare åtkomstregister

**PE-8.b** organisationen granskar besökare åtkomstregister [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Besökare med en godkänd åtkomstbegäran har deras åtkomst poster granskat när deras identifiering verifieras mot en form av myndigheter som har utfärdat ID eller Microsoft utfärdas bricka. Enligt beskrivningen i PE 3 åtföljda besökare alltid på datacentret. |


 ### <a name="nist-800-53-control-pe-8-1"></a>NIST 800 53 kontrollen PE-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Besökare åtkomstregister | Automatisk poster Underhåll / granska

**PE-8.1** organisationen använder automatisk mekanismer för att underlätta underhåll och genomgång av besökare åtkomstregister.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure underhåller datacenter åtkomstregister i DCAT i form av godkända DCAT begäranden. DCAT begäranden kan endast godkännas av DCM-teamet. Åtkomstnivåer inom datacentret tilldelas och hanteras i DCAT. Datacenter åtkomst granskas kvartalsvis. All åtkomst till Azure-Datacenter inte har registrerats i DCAT och är tillgänglig för framtida undersökningar som möjligt. Besökare måste åtföljas vid alla tidpunkter. Den eskort åtkomst inom datacentret loggas i larmet övervakning av system och om nödvändigt kan korreleras till besökscentret för framtida granskning. Besökare granskas kontinuerligt genom tilldelade eskort och kontroll rum handledarens via CCTV och larm övervakningen. Besökare har inte åtkomst och måste åtföljas av deras escorts vid alla tidpunkter. |


 ## <a name="nist-800-53-control-pe-9"></a>NIST 800 53 kontrollen PE-9

#### <a name="power-equipment-and-cabling"></a>Power utrustning och kablar

**PE-9** organisationen skyddar power utrustning och power kablar för systemet från skador och -destruktion.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure tillhandahåller skyddande blanksteg och lämpliga etiketter för kablar. Microsoft Azure infrastructure utrustning, kablar, elektriska rader och säkerhetskopiering generatorer måste placeras i miljöer som har utformats för att skyddas från miljön risker som stöld, brand, sprängämnen, rök, vattenstämplar, damm, vibration, jordbävning, skadliga kemikalier, elektriska störningar, strömavbrott, elektriska störningar (toppar). Alla bärbara onlinetjänster tillgångar (t.ex., rack, servrar, nätverksenheter) måste vara låst eller fästade på plats för att ge skydd mot stöld eller flytt skador. Ström och information system-kablar i Microsoft Azure-miljön med etiketten på rätt sätt och skyddas mot avlyssning eller skada. Ström och information system-kablar är åtskilda från varandra på alla distributionsplatser i en miljö för att undvika störningar. |


 ## <a name="nist-800-53-control-pe-10a"></a>NIST 800 53 kontrollen PE-10.a

#### <a name="emergency-shutoff"></a>Nödfall avslutning

**PE-10.a** organisationen gör möjligheten att stänga av strömmen till informationssystem eller enskilda system komponenter i nödfall.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure har installerats nödsituation Power av (EPO) knappar på platser inom datacentret som krävs av lokala fire-kod. Datacenter-designen kräver inte längre EPO knapparna i vissa Microsoft Azure hanterade datacenter. |


 ## <a name="nist-800-53-control-pe-10b"></a>NIST 800 53 kontrollen PE-10.b

#### <a name="emergency-shutoff"></a>Nödfall avslutning

**PE-10.b** organisationen placerar nödfall avslutning växlar eller enheter i [tilldelning: organisation definierats plats med informationssystem- eller systemkomponent] att underlätta säkert och enkelt åtkomst för personal.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. EPO knappar placeras strategiskt för att tillåta för aktivering i nödfall. EPO knappar kan placeras i colocations, bemannade verksamhet åtgärden datacenter (FOCs), eller som krävs av lokala fire-kod. |


 ## <a name="nist-800-53-control-pe-10c"></a>NIST 800 53 kontrollen PE-10.c

#### <a name="emergency-shutoff"></a>Nödfall avslutning

**PE-10.c** organisationen skyddar nödfall avslutning strömförsörjningsfunktion från obehörig aktivering.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. För att förhindra oavsiktlig aktivering kan EPO knappar ha ett skyddande hölje, kräver dubbla aktivering eller använda ett akustiskt larm som en varning innan aktivering. Dessutom är EPO knapparna under video övervakning. |


 ## <a name="nist-800-53-control-pe-11"></a>NIST 800 53 kontrollen PE-11

#### <a name="emergency-power"></a>Nödfall Power

**PE-11** organisationen ger en kortsiktig avbrottsfri elkälla att underlätta [markeringen (en eller flera): kunna avslutas av informationssystem; övergången informationssystem till långsiktig alternativa power] i händelse av en primära källan strömavbrott.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure har implementerat nödfall power genom att skydda datacenter utrustning och kretsar med en avbrottsfri ange (UPS)-system som ger en kortsiktig strömförsörjning för att ge kraft förrän generatorer kan anslutas. |


 ### <a name="nist-800-53-control-pe-11-1"></a>NIST 800 53 kontrollen PE-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Nödfall Power | Långsiktig alternativa strömförsörjning - Minimal driftskapacitet

**PE-11 (1)** organisationen innehåller en långsiktig alternativa strömförsörjning för det system som kan upprätthålla minimalt krävs driftskapacitet vid en utökad förlust av primära strömkällan.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure har implementerat en långsiktig alternativa strömförsörjning för det system som kan upprätthålla en minimikravet driftskapacitet när en utökad förlust av primära strömkällan inträffar. När power misslyckas eller sjunker till en oacceptabel spänning nivå, startar direkt i avbrottsfri elkälla (UPS) system och tar över belastningen power. Detta ger tillräckligt med ström för att köra servrarna tills generatorer kan ta över. Nödgeneratorer ange säkerhetskopiera power för långvariga avbrott och för planerat underhåll och kan arbeta datacenter med lokala bränsle reserver vid en naturkatastrof. Azure underhåller diesel generator på många av våra datacenter. Säkerhetskopiering generatorer används när det är nödvändigt för att upprätthålla stabiliteten för rutnät eller i extraordinära reparation och underhåll situationer som kräver att vi ska ta våra Datacenter av power rutnät. |


 ## <a name="nist-800-53-control-pe-12"></a>NIST 800 53 kontrollen PE-12

#### <a name="emergency-lighting"></a>Nödfall ljus

**PE-12** organisationen använder och underhåller automatisk nödfall ljus för det system som aktiveras vid ett strömavbrott eller avbrott och som omfattar nödfall avslutas och evakueringsvägar inom funktionen.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure-datacenter (hyrd och fullständigt hanterade) implementera nödfall ljus i form av arbetet nödfall ljus på dedikerade kretsar säkerhetskopieras av UPS och generator system (se PE-11). Automatisk nödfall ljus implementeras längs alla evakueringsvägar nödfall avslutar och inuti colocations enligt de nationella brand och skydd Association (NFPA) livslängd säkerhet kod. I händelse av att strömförsörjningen tappas bort, växlar nödfall ljus automatiskt till power som tillhandahålls av UPS och generator system. Nödfall ljus system inom Microsoft Azure-Datacenter genomgå rutinunderhåll för att säkerställa att de fortfarande är i rätt fungerande skick. |


 ## <a name="nist-800-53-control-pe-13"></a>NIST 800 53 kontrollen PE-13

#### <a name="fire-protection"></a>Fire-skydd

**PE-13** organisationen använder och underhåller brand Undertryckning och identifiera enheter/system för systemet som stöds av en oberoende energikälla.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure har implementerat brandskydd genom att installera system för fire-identifiering och brand Undertryckning på Microsoft Azure-datacenter. <br /> Microsoft Azure-Datacenter implementera robust brand identifieringsmetoder. Microsoft Azure brand skydd strategi innehåller användningen av fotoelektrisk rökdetektorer installerats under våningen och på tak som är integrerade med sprinklersystem för skydd. Dessutom är Xtralis VESDA (mycket tidig brandgas identifiering apparater) system i varje samordning som övervakar luften. VESDA enheter är mycket känslig luften provtagning System installerat i flera värdefulla mellanslag. VESDA enheter gör det möjligt att en undersökning svar innan larm brand identifiering. <br /> 'Hämtar station' brand larm rutor är installerade i hela Datacenter för manuell brand larmmeddelande. Brandsläckare är finns i hela datacentret och korrekt granskas, servas och märkta årligen. Säkerhetspersonalen patrols alla utvecklingsområden flera gånger varje SKIFT. Datacenter supportpersonal utför en daglig plats hanteringspaketen säkerställt alla brand titta på krav uppfylls. <br /> Områden som innehåller känsliga elektrisk utrustning (colocations MDFs, etc.) skyddas av sprinklersystem för dubbla hopkoppling före åtgärden (torr pipe). Torr pipe sprinklerna är ett två steg före åtgärden system som kräver både en sprinkler head aktivering (på grund av värme) samt beståndsdelar identifiering att släppa vattenstämplar. Sprinkler head aktiveringen Frigör lufttrycket i pipes där pipes med vattenstämplar. Vattenstämplar släpps när en detektor rök- eller också har aktiverats. <br /> Startar identifiering/Undertryckning och nödfall ljus kabelanslutna på datacenter UPS och generator system för en redundant strömförsörjning källa. |


 ### <a name="nist-800-53-control-pe-13-1"></a>NIST 800 53 kontrollen PE-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>Brand skydd | Identifiering av enheter / system

**PE-13 (1)** organisation sådana utlösa identifiering enheter/system för systemet som aktiverar automatiskt och meddela [tilldelning: personal som definierats av organisationen eller roller] och [tilldelning: organisation definierats nödfall svarare] om brand.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure sådana utlösa identifiering enheter/system för systemet som aktiverar automatiskt och meddela datacenter personal tillsammans med nödfall svarare om brand. I händelse av att en av identifieringsmetoder brand aktiveras i alla samordning meddelas avdelningen lokala brand automatiskt via brand larmsystem. Dessutom är system för brand skydd och brand identifiering knutna till säkerhetssystem meddela den lokala anläggningen och säkerhetspersonal. |


 ### <a name="nist-800-53-control-pe-13-2"></a>NIST 800 53 kontrollen PE-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>Brand skydd | Undertryckning enheter / system

**PE-13.2** organisation sådana brand Undertryckning enheter/system för det system som ger automatisk anmälan av alla aktivering [tilldelning: personal som definierats av organisationen eller roller] och [tilldelning: organisation definierats nödfall svarare].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. I händelse av att ett av brand Undertryckning system är aktiverad i datacentret, meddelas avdelningen lokala brand automatiskt via brand larmsystem. Dessutom är system för brand skydd och brand identifiering knutna till säkerhetssystem meddela den lokala anläggningen och säkerhetspersonal. |


 ### <a name="nist-800-53-control-pe-13-3"></a>NIST 800 53 kontrollen PE-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>Brand skydd | Automatisk brand Undertryckning

**PE-13 (3)** organisationen använder en automatisk brand Undertryckning kapaciteten för systemet när funktionen får du inte hjälp kontinuerligt.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure-Datacenter är personal 24 x 7. Fire Undertryckning system engagera automatiskt utan manuella åtgärder när en brand larm situation har upptäckts. |


 ## <a name="nist-800-53-control-pe-14a"></a>NIST 800 53 kontrollen PE-14.a

#### <a name="temperature-and-humidity-controls"></a>Temperatur- och Fuktighetskonsekvens kontroller

**PE-14.a** organisationen underhåller temperatur- och fuktighetskonsekvens nivåer inom funktionen där systemet som finns på [tilldelning: organisation definierats acceptabel nivå].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure underhåller temperatur- och fuktighetskonsekvens nivåer i enlighet med riktlinjer American Society av uppvärmning, Refrigerating och luftkonditionering Engineers (ASHRAE). Nivåer för temperatur- och fuktighetskonsekvens övervakas kontinuerligt av datacentrets byggnad Management System (BMS). |


 ## <a name="nist-800-53-control-pe-14b"></a>NIST 800 53 kontrollen PE-14.b

#### <a name="temperature-and-humidity-controls"></a>Temperatur- och Fuktighetskonsekvens kontroller

**PE-14.b** organisationen övervakar temperatur- och fuktighetskonsekvens [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Vid Microsoft Azure-Datacenter temperatur- och fuktighetskonsekvens nivåer som ska övervakas kontinuerligt genom att skapa Management System (BMS). Datacenter personal övervaka BMS från verksamhet Operations Center (FOC) så att de kan hantera temperatur- och fuktighetskonsekvens inom datacentret innan eventuella larm punkter överskrids. |


 ### <a name="nist-800-53-control-pe-14-2"></a>NIST 800 53 kontrollen PE-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Temperatur- och Fuktighetskonsekvens kontroller | Övervakning med larm / meddelanden

**PE-14 (2)** organisationen använder temperatur och fuktighet övervakning som tillhandahåller ett larm eller meddelanden om ändringar som kan vara skadliga för personal eller utrustning.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Vid Microsoft Azure-Datacenter temperatur- och fuktighetskonsekvens nivåer som ska övervakas kontinuerligt genom att skapa Management System (BMS). Datacenter personal övervaka BMS från verksamhet Operations Center (FOC) så att de kan hantera temperatur- och fuktighetskonsekvens inom datacentret innan eventuella larm punkter överskrids. |


 ## <a name="nist-800-53-control-pe-15"></a>NIST 800 53 kontrollen PE-15

#### <a name="water-damage-protection"></a>Vattenstämplar skada skydd

**PE-15** organisationen skyddar systemet från skador som uppstår till följd av läckage av vattenstämplar genom att tillhandahålla master avslutning eller isolering ventiler som är tillgänglig, fungerar på rätt sätt och kända personal.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure tillhandahåller vattenstämplar-läcka identifiering i områden med risk för läckage av vattenstämplar (t.ex. luft hanterare enheter). Fire Undertryckning system har också läcka identifiering larm som övervakas. Systemets vattenstämplar-läcka identifiering är integrerad med lokal larm och notification system. Sprinklersystem i datacenter är zon. Datacenter-personal är bekanta med nödfall procedurer som kräver användning av vattenstämplar avslutning ventiler och deras platser. Sprinkler riser-kort har möjlighet att stängas ner separat eller som en grupp via gate ventiler. Alla sprinklerna i kritiska utrymme är dubbla hopkoppling före åtgärdstyp sprinklerna som kräver två typer av aktivering innan flödet initieras. Tryck på sprinkleranläggningen övervakas och mot läckage av vattenstämplar är tom. |


 ### <a name="nist-800-53-control-pe-15-1"></a>NIST 800 53 kontrollen PE-15 (1)

#### <a name="water-damage-protection--automation-support"></a>Vatten skada skydd | Stöd för automatisering

**PE-15 (1)** organisationen använder automatisk mekanismer för att detektera förekomst av vattenstämplar i närheten av informationssystem och aviseringar [tilldelning: personal som definierats av organisationen eller roller].

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure använder automatisk mekanismer för att identifiera vattenstämplar förekomst i datacenter och varnar datacenter personal. Azure tillhandahåller vattenstämplar-läcka identifiering i områden med risk för läckage av vattenstämplar (t.ex. luft hanterare enheter). Fire Undertryckning system har också läcka identifiering larm som övervakas. Systemets vattenstämplar-läcka identifiering är integrerad med lokal larm och notification system. Tryck på sprinkleranläggningen övervakas och mot läckage av vattenstämplar är tom. |


 ## <a name="nist-800-53-control-pe-16"></a>NIST 800 53 kontrollen PE-16

#### <a name="delivery-and-removal"></a>Leverans och borttagning

**PE-16** organisationen godkänner, övervakar och styr [tilldelning: organisation användardefinierade typer av information systemkomponenter] avslutar funktionen och underhåller poster dessa objekt.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementerar det här kravet dina kunders räkning. Microsoft Azure implementerar av vad som är tillåtet att ange och avsluta datacentret. Alla system komponenter tillgångar spåras i verktyget hanteringsdatabasen tillgången. |


 ## <a name="nist-800-53-control-pe-17a"></a>NIST 800 53 kontrollen PE-17.a

#### <a name="alternate-work-site"></a>Alternate Work Site

**PE-17.a** organisationen använder [tilldelning: organisation definierats säkerhetsåtgärder] på alternativa arbetsplatser.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå alternativa arbete plats bestämmelser kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-pe-17b"></a>NIST 800 53 kontrollen PE-17.b

#### <a name="alternate-work-site"></a>Alternate Work Site

**PE-17.b** organisationen utvärderar som möjligt, effektivitet på säkerhet kontroller på alternativa arbetsplatser.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå alternativa arbete plats bestämmelser kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-pe-17c"></a>NIST 800 53 kontrollen PE-17.c

#### <a name="alternate-work-site"></a>Alternate Work Site

**PE-17.c** organisationen är ett sätt för anställda att kommunicera med information security personal vid säkerhetsincidenter eller problem.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå alternativa arbete plats bestämmelser kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-pe-18"></a>NIST 800 53 kontrollen PE-18

#### <a name="location-of-information-system-components"></a>Platsen för Information systemkomponenter

**PE-18** organisationen placerar information systemkomponenter i funktionen för att minimera eventuella skador från [tilldelning: organisation definierats fysisk och miljösäkerhet risker] och för att minimera risken för obehörig åtkomst.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Azure implementerar strategiska datacenter Designmetoden att uppfylla platsen för kontroll för system-komponenter. Alla Microsoft online services-utrustning placeras på platser som har utformats för att skyddas från miljön risker som stöld, brand, sprängämnen, rök, vattenstämplar, damm, vibrationer, jordbävning, skadliga kemikalier, elektriska störningar strömavbrott, elektriska störningar (toppar) och strålning. Lokal och infrastruktur har implementerat seismisk kompletteringar för skydd mot risker för miljön. Alla samordning och MDF rum skyddas av åtkomstkontroll, larm och video. Funktionen är också patrulleras av säkerhet polis 24 x 7. Alla bärbara Azure tillgångar är låst eller fästade på plats för att ge skydd mot stöld eller flytt skador. |


