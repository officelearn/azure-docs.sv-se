---
title: "Azure-säkerhet och efterlevnad modell - FedRAMP Web Applications Automation - granskning och ansvar"
description: FedRAMP Web Applications Automation - kontroll och ansvar
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: c37d027882882c637585d745ce81111ba30f6257
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="audit-and-accountability-au"></a>Granskning och Accountability (AU)

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Läs NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

## <a name="nist-800-53-control-au-1"></a>NIST 800 53 kontrollen AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Granskning och Accountability principer och procedurer

**Australien 1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] en princip för granskning och accountability som åtgärdar syfte, scope, roller, ansvar, management åtagande samordning av organisationens entiteter och efterlevnad; och procedurer för att underlätta granskning och accountability princip och associerade granskning och accountability kontroller. och granskar och uppdaterar den aktuella principen för granskning och accountability [tilldelning: organisation definierats frekvens]; och gransknings-och accountability [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå granskning och accountability principer och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-2a"></a>NIST 800 53 kontrollen AU-2.a

#### <a name="audit-events"></a>Granska händelser

**Australien 2.a** organisationen anger att systemet kan granska följande händelser: [tilldelning: organisation definierats granskningsbara händelser].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Granska kapaciteten för det här utkastet tillhandahålls av Azure-Monitor och Log Analytics-tjänsten i OMS. Azure övervakning ger detaljerad granskningsloggar om aktiviteter som är associerade med distribuerade resurser. Dessa och OS-nivå loggar samlas in av logganalys och lagras i OMS-databasen. Logganalys korrelerar granskningsdata över resurser som distribueras med den här lösningen och kan utökas till kunden distribuerade webbprogrammet. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-2b"></a>NIST 800 53 kontrollen AU-2.b

#### <a name="audit-events"></a>Granska händelser

**Australien 2.b** organisationen samordnar revisionen säkerhet med andra organisationens enheter kräver gransknings-relaterad information att förbättra ömsesidigt stöd och hjälper valet av granskningsbara händelser.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på en upprättad på företagsnivå process som bestämmer granskningsbara händelser. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-2c"></a>NIST 800 53 kontrollen AU-2.c

#### <a name="audit-events"></a>Granska händelser

**Australien 2.c** organisationen ger en grund för varför granskningsbara händelser anses vara tillräcklig för att stödja efter fakta undersökningar av säkerhetsincidenter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Händelser som granskas av den här utkast innehåller information som är tillräckligt för att avgöra när händelser inträffar, källan för händelsen, resultatet av händelsen och annan information som har stöd för undersökning av säkerhetsincidenter. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-2d"></a>NIST 800 53 kontrollen AU-2.d

#### <a name="audit-events"></a>Granska händelser

**Australien 2.d** organisationen bestämmer att följande händelser som ska granskas inom systemet: [tilldelning: organisation definierats granskade händelser (delmängden av granskningsbara händelser som definierats i Australien 2 a.) tillsammans med frekvensen av (eller situation requiring) granskning för varje identifierad händelse].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Händelser som granskas av den här utkast innehåller de granskas av Azure aktivitetsloggar för distribuerade resurser, OS-nivå loggar, Active Directory-loggar och SQL Server-loggar. Kunder kan välja ytterligare händelser som ska granskas för att uppfylla uppdrag behov. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-2-3"></a>NIST 800 53 kontrollen AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Granska händelser | Granskning och uppdateringar

**Australien-2 (3)** organisationen granskar och uppdaterar granskade händelser [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlitar sig en upprättad på företagsnivå regelbunden granskning och uppdatera process för en definierad uppsättning granskade händelser. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-3"></a>NIST 800 53 kontrollen AU-3

#### <a name="content-of-audit-records"></a>Innehåll av granskningsposter

**Australien 3** granskningsposter som innehåller information som fastställer vilken typ av händelse uppstod när händelsen inträffade, där händelsen inträffade, källan för händelsen, resultatet av händelsen och identiteten för någon genereras information enskilda användare eller ämnen som är associerade med händelsen.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet är beroende av inbyggda audit funktionerna i Azure, Windows Server och SQL Server. Dessa granska lösningar avbilda granskningsposter med tillräckligt detaljerat för att uppfylla kraven för den här kontrollen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-3-1"></a>NIST 800 53 kontrollen Australien – 3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Innehåll av granskningsposter | Ytterligare granskningsinformation

**Australien – 3 (1)** information genereras granskningsposter som innehåller följande information: [tilldelning: organisation definierade ytterligare, mer detaljerad information].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Azure aktivitetsloggen händelser använder en detaljerad schema som innehåller fält för fler än 20 typer av granskningsinformation. Förutom aktivitetsloggen distribuerar det här utkastet logganalys lösningen i OMS som stöder en mängd olika datakällor, inklusive Windows-loggar, Linux loggar, Azure-diagnostik loggar och kunden loggar.  |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-3-2"></a>NIST 800 53 kontrollen Australien – 3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Innehåll av granskningsposter | Centraliserad hantering av planerad Audit spela in innehåll

**Australien-3.2** informationssystem tillhandahåller centraliserad hantering och konfiguration av innehållet som ska läggas till i granskningsposter som genererats av [tilldelning: organisation definierats information systemkomponenter].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Alla virtuella datorer som distribueras med den här modell är anslutna till den distribuerade Active Directory-domänen. En grupprincip som kan konfigureras för att centralt hantera systemkonfigurationen OS-nivå audit implementeras i alla domänanslutna virtuella datorer. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-4"></a>NIST 800 53 kontrollen AU-4

#### <a name="audit-storage-capacity"></a>Granska lagringskapacitet

**Australien 4** organisationen allokerar audit poster lagringskapacitet i enlighet med [tilldelning: organisation definierats audit poster lagringskraven].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet allokerar tillräcklig kapacitet att behålla granskningsposter för ett år. Alla granskningsposter samlas in av logganalys som är konfigurerad för ett år kvarhållning. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-5a"></a>NIST 800 53 kontrollen AU-5.a

#### <a name="response-to-audit-processing-failures"></a>Svaret ska granskas bearbetningsfel

**Australien 5.a** varningarna information [tilldelning: personal som definierats av organisationen eller roller] vid revision bearbetningsfel.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Status för tjänsten för Azure-Monitor och Log Analytics finns på statuswebbplatsen Azure och bladet tjänsten för hälsotillstånd i Azure-portalen. Aviseringar kan konfigureras via Log Analytics för andra typer av audit bearbetningsfel. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-5b"></a>NIST 800 53 kontrollen AU-5.b

#### <a name="response-to-audit-processing-failures"></a>Svaret ska granskas bearbetningsfel

**Australien 5.b** informationssystem utförs följande ytterligare åtgärder: [tilldelning: organisation definierats åtgärder som ska utföras (t.ex. Stäng informationssystem, ersätta äldsta granskningsposter, avbryta genereringen av granskningsposter)].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Alla granskningsposter som genererats av resurser som distribueras med den här modell som samlas in av logganalys och bevaras under en period av ett år. Lagringsutrymme för lagringen audit poster dynamiskt allokerade säkerställa tillräcklig kapacitet är tillgänglig. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-5-1"></a>NIST 800 53 kontrollen AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Svaret ska granskas bearbetningsfel | Granska lagringskapacitet

**Australien-5 (1)** informationssystem ger en varning [tilldelning: personal som definierats av organisationen, roller och/eller platser] i [tilldelning: definierad tidsperiod organisation] när allokerade audit poster lagringsvolym når [ Tilldelning: organisation definierats procent] databas som högsta gransknings-och registrera lagringskapacitet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Alla granskningsposter som genererats av resurser som distribueras med den här modell som samlas in av logganalys och bevaras under en period av ett år. Lagringsutrymme för lagringen audit poster dynamiskt allokerade säkerställa tillräcklig kapacitet är tillgänglig. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-5-2"></a>NIST 800 53 kontrollen AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Svaret ska granskas bearbetningsfel | Aviseringar i realtid

**Australien-5 (2)** informationssystem ger en avisering i [tilldelning: organisation definierats realtid period] till [tilldelning: personal som definierats av organisationen, roller och/eller platser] när följande granskningshändelser fel inträffar: [tilldelning: organisation definierats fel granskningshändelser som kräver aviseringar i realtid].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Status för tjänsten för Azure finns på bladet tjänsten för hälsotillstånd i Azure-portalen. Aviseringar kan konfigureras via Log Analytics för andra typer av audit bearbetningsfel. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-6a"></a>NIST 800 53 kontrollen AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>Granska granskning, analys och rapportering

**Australien 6.a** organisationen granskar och analyserar information system granskningsposter [tilldelning: organisation definierats frekvens] för tecken på [tilldelning: organisation definierats olämpliga eller ovanlig aktivitet].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska och analysera granskningsposter av kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara). |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-6b"></a>NIST 800 53 kontroll-AU-6 b

#### <a name="audit-review-analysis-and-reporting"></a>Granska granskning, analys och rapportering

**Australien-6 b** organisationen rapporterar resultaten till [tilldelning: personal som definierats av organisationen eller roller].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att rapportera resultaten av olämpliga eller ovanlig aktivitet (definieras i Australien 06.a) på kunden distribuerade resurser. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-6-1"></a>NIST 800 53 kontrollen Australien – 6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Granska granskning, analys och rapportering | Integrering

**Australien-6.1** organisationen använder automatisk mekanismer för att integrera granskning granska, analys och rapportering processer för att stödja organisationens processer för undersökning och svar på misstänkta aktiviteter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på företagsnivå central granskning granska, analys- och rapporteringsfunktion. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-6-3"></a>NIST 800 53 kontrollen Australien – 6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Granska granskning, analys och rapportering | Korrelera Audit databaser

**Australien-6.3** organisationen analyserar och korrelerar granskningsloggarna mellan olika databaser att få precisa medvetenhet för hela organisationen.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar logganalys-lösning i OMS att centralisera granskningsdata över distribuerade resurser som stödjer precisa medvetenhet för hela organisationen. Kunder kan välja att ytterligare integrera logganalys med andra system. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-6-4"></a>NIST 800 53 kontrollen Australien – 6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Granska granskning, analys och rapportering | Central granskning och analys

**Australien-6.4** informationssystem ger möjlighet att centralt granska och analysera granskningsposter från flera komponenter i systemet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar logganalys-lösning i OMS att centralisera granskningsdata över distribuerade resurser, stödjande central granskning, analys och rapportering. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-6-5"></a>NIST 800 53 kontrollen Australien – 6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Granska granskning, analys och rapportering | Integration / genomsöka och kapacitet för övervakning

**Australien – 6 (5)** organisationen integrerar analys av granskningsposter med analyser av [markeringen (en eller flera): säkerhetsproblem genomsökning information; prestandadata; informationssystem övervakningsinformation; [Tilldelning: organisation definierats data/information som samlas in från andra källor]] för att förbättra ytterligare möjligheten att identifiera olämpliga eller ovanlig aktivitet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar lösningen OMS säkerhets- och granskning. Den här lösningen ger en omfattande vy av säkerhet. Säkerhet och granska instrumentpanelen ger övergripande inblick i säkerhetsläget för distribuerade resurser med hjälp av tillgängliga data mellan distribuerade OMS-lösningar, integrera loggdata och säkerhetsproblem data från baslinjen och korrigering. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-6-6"></a>NIST 800 53 kontrollen Australien – 6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Granska granskning, analys och rapportering | Korrelation med fysiska övervakning

**Australien – 6 (6)** organisationen korrelerar information från granskningsposter med information som erhållits från övervakning fysisk åtkomst för att ytterligare förbättra möjligheten att identifiera misstänkta olämpliga, onormal eller malevolent aktivitet.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Provider (Microsoft Azure)** | Microsoft Azure SIM-teamet använder relaterade fysiska övervakningsdata och korrelerar med granskningsposter för att avgöra om det finns kopplade logiska överträdelse eller misstänkt beteende när fysiska incidenter identifieras. |


 ### <a name="nist-800-53-control-au-6-7"></a>NIST 800 53 kontrollen Australien – 6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Granska granskning, analys och rapportering | Tillåtna åtgärder

**Australien – 6 (7)** organisationen anger tillåtna åtgärder för varje [markeringen (en eller flera): information systemprocessen; rollen; användare] som är associerade med granskningen, analys och rapportering av granskningsinformation.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Windows-datorer som distribueras med den här utkast implementera OS-behörighet som begränsar de åtgärder som en användare kan utföra med avseende granskningsinformation. I Azure, användare eller grupper av användare kan tilldelas till roller (t.ex. ägare, deltagare, reader eller en anpassad roll) för att begränsa åtgärder som är tillgängliga med avseende på några resurser eller distribueras lösningar, inklusive logganalys.  |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-6-10"></a>NIST 800 53 kontrollen Australien – 6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Granska granskning, analys och rapportering | Granska nivån justering

**Australien – 6 (10)** organisationen justerar nivån för granskning granska, analys och rapportering i informationssystemet om det finns en ändring i risken för lag tvingande information, affärsinformation eller andra trovärdig datakällor information.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att justera säkerhetsnivån granskning granska, analys och rapportering för kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara) när det finns en ändring i risk baserat på informationen som ges enligt lag tvingande, intelligence eller andra trovärdig källor. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-7a"></a>NIST 800 53 kontrollen AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>Granska minska och skapa rapporter

**Australien 7.a** informationssystem ger en audit minskning och rapporten generationens funktion som har stöd för på begäran gransknings-och granskning, analys och rapporteringskrav och efter fakta undersökningar av säkerhetsincidenter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar logganalys-lösning i OMS. Log Analytics tillhandahåller övervakningstjänster för OMS genom att samla in data från hanterade resurser i en central databas. När data har samlats in är de tillgängliga för avisering, analys och export. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-7b"></a>NIST 800 53 kontrollen AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>Granska minska och skapa rapporter

**Australien 7.b** informationssystem ger en audit rapport och minskning generation som inte ändrar det ursprungliga innehållet eller tid sorteringen av granskningsposter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar logganalys-lösning i OMS. Log Analytics tillhandahåller övervakningstjänster för OMS genom att samla in data från hanterade resurser i en central databas. Innehåll och tid sorteringen av granskningsposter ändras inte när samlas in av logganalys. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-7-1"></a>NIST 800 53 kontrollen AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Granska minskning och rapportgenerering | Automatisk bearbetning

**Australien-7.1** informationssystem ger möjlighet att bearbeta granskningsposter för händelser utifrån [tilldelning: organisation definierats audit fält inom granskningsposter].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar logganalys-lösning i OMS. Log Analytics tillhandahåller övervakningstjänster för OMS genom att samla in data från hanterade resurser i en central databas. När data har samlats in är de tillgängliga för avisering, analys och export. Log Analytics innehåller ett kraftfullt frågespråk för att extrahera data som lagras i databasen. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-8a"></a>NIST 800 53 kontrollen AU-8.a

#### <a name="time-stamps"></a>Tidsstämplar

**Australien 8.a** information används internt systemklockorna för att generera tidsstämplar för granskningsposter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Resurser som distribueras med den här utkast använder interna systemklockorna för att generera tidsstämplar för granskningsposter. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-8b"></a>NIST 800 53 kontrollen AU-8.b

#### <a name="time-stamps"></a>Tidsstämplar

**Australien 8.b** informationssystemet registrerar tidsstämplar för granskningsposter som kan mappas till Coordinated Universal Time (UTC) eller Greenwich Mean Time (GMT) och uppfyller [tilldelning: organisation definierats Granulariteten för tidmätning].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Resurser som distribueras med den här utkast använder interna systemklockorna för att generera tidsstämplar för granskningsposter. Tidsstämplar registreras i UTC. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-8-1a"></a>NIST 800 53 kontrollen Australien 8 (1) en

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Tidsstämpel | Synkronisering med auktoritär tidskälla

**Australien-8.1 schemanivån** information jämförs systemklockorna intern information [tilldelning: organisation definierats frekvens] med [tilldelning: organisation definierats auktoritär tidskälla].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Resurser som distribueras med den här utkast använder interna systemklockorna för att generera tidsstämplar för granskningsposter. Internt systemklockorna har konfigurerats för synkronisering med en auktoritär tidskälla. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-8-1b"></a>NIST 800 53 kontrollen Australien 8 (1) .b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Tidsstämpel | Synkronisering med auktoritär tidskälla

**Australien-8.1 .b** informationssystemet synkroniserar interna systemklockorna till auktoritär tidskälla när tidsskillnaden är större än [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Resurser som distribueras med den här utkast använder interna systemklockorna för att generera tidsstämplar för granskningsposter. Internt systemklockorna har konfigurerats för synkronisering med en auktoritär tidskälla. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-9"></a>NIST 800 53 kontrollen AU-9

#### <a name="protection-of-audit-information"></a>Skydd av granskningsinformation

**Australien 9** informationssystem skyddar information och granska granskningsverktygen från obehörig åtkomst, ändras eller tas bort.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Logiska åtkomstkontroller för att skydda granskningsinformation och verktyg i det här utkastet från obehörig åtkomst, ändras eller tas bort. Azure Active Directory tillämpar godkända logiska åtkomst med hjälp av rollbaserad gruppmedlemskap. Möjlighet att granska information och använda granskningsverktygen kan vara begränsad till användare som kräver dessa behörigheter. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-9-2"></a>NIST 800 53 kontrollen AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Skydd av granskningsinformation | Granska säkerhetskopiering på separata fysiska system / komponenter

**Australien-9.2** systemet som säkerhetskopierar granskningsposter [tilldelning: organisation definierats frekvens] till ett fysiskt olika system eller systemkomponent än systemet eller komponenten granskas.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar Log Analytics-tjänsten i OMS. Distribuera virtuella datorer och Azure diagnostics storage-konton är anslutna datakällor till logganalys och sparade separat från sina ursprung. Data samlas in av OMS i nära realtid. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-9-3"></a>NIST 800 53 kontrollen AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Skydd av granskningsinformation | Kryptografiska skydd

**Australien-9.3** informationssystem implementerar kryptografiska metoder för att skydda information och granska granskningsverktygen.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar Log Analytics-tjänsten i OMS. Logganalys garanterar att inkommande data från en betrodd källa genom att verifiera certifikat och dataintegritet med Azure-autentisering. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-9-4"></a>NIST 800 53 kontrollen AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Skydd av granskningsinformation | Åtkomst av delmängd av Privilegierade användare

**Australien-9 (4)** organisationen tillåter åtkomst till hantering av adressgranskning enbart [tilldelning: organisation definierad delmängd av Privilegierade användare].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Logiska åtkomstkontroller för att skydda granskningsinformation och verktyg i det här utkastet från obehörig åtkomst, ändras eller tas bort. Azure Active Directory tillämpar godkända logiska åtkomst med hjälp av rollbaserad gruppmedlemskap. Möjlighet att granska information och använda granskningsverktygen kan vara begränsad till användare som kräver dessa behörigheter.
 |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-10"></a>NIST 800 53 kontrollen 10-Australien

#### <a name="non-repudiation"></a>Oavvislighet

**Australien 10** systemet som skyddar mot en enskild person (eller processen fungerar för en enskild) att neka har utfört [tilldelning: organisation definierats åtgärder som ska omfattas av oavvislighet].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Granska kapaciteten för det här utkastet tillhandahålls av Azure-Monitor och Log Analytics-tjänsten i OMS. Azure övervakning ger detaljerad granskningsloggar om aktiviteter som är associerade med distribuerade resurser. Dessa och OS-nivå loggar samlas in av logganalys och lagras i OMS-databasen. Dessa loggar finns detaljerade register över information systemhändelser och skyddar mot oavvislighet. Dessutom är logga data begränsade använda rollbaserad åtkomstkontroll för att förhindra unauthored ändras eller tas bort av loggdata. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-11"></a>NIST 800 53 kontrollen AU-11

#### <a name="audit-record-retention"></a>Gransknings-och registrera kvarhållning

**Australien 11** organisationen behåller granskningsposter för [tilldelning: definierad tidsperiod som är konsekventa med poster bevarandeprincip organisation] att ge stöd för efter fakta undersökningar av säkerhetsincidenter och för att uppfylla regelverk och organisationsinformation krav på datalagring.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar Log Analytics-tjänsten i OMS. Log Analytics tillhandahåller övervakningstjänster för OMS genom att samla in data från hanterade resurser i en central databas. När samlats in behålls data för ett år per konfiguration för logganalys. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-12a"></a>NIST 800 53 kontrollen AU-12.a

#### <a name="audit-generation"></a>Granska Generation

**Australien 12.a** informationssystem ger audit poster generation möjlighet till granskningsbara händelser som definierats i Australien 2 en. vid [tilldelning: organisation definierats information systemkomponenter].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Händelser som granskas av den här utkast innehåller de granskas av Azure aktivitetsloggar för distribuerade resurser, OS-nivå loggar, Active Directory-loggar och SQL Server-loggar. Kunder kan välja ytterligare händelser som ska granskas för att uppfylla uppdrag behov. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-12b"></a>NIST 800 53 kontrollen AU-12.b

#### <a name="audit-generation"></a>Granska Generation

**Australien 12.b** tillåter informationssystemet [tilldelning: personal som definierats av organisationen eller roller] att välja vilka granskningsbara händelser som ska granskas av specifika komponenter i systemet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Åtkomst till granska funktioner begränsas med rollbaserad åtkomstkontroll i Azure och på den virtuella datorn OS-nivå. Konfigurationen av händelser som ska granskas av resurser som distribueras med den här modell kan konfigureras av användare med lämpliga rollbaserad auktorisering. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-au-12c"></a>NIST 800 53 kontrollen AU-12.c

#### <a name="audit-generation"></a>Granska Generation

**Australien 12.c** information genereras granskningsposter för händelser som definierats i Australien 2.d. med det innehåll som definieras i Australien 3.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Händelser som granskas av den här utkast innehåller de granskas av Azure aktivitetsloggar för distribuerade resurser, OS-nivå loggar, Active Directory-loggar och SQL Server-loggar. Kunder kan välja ytterligare händelser som ska granskas för att uppfylla uppdrag behov. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-12-1"></a>NIST 800 53 kontrollen Australien – 12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Granska Generation | Systemomfattande / tid-korrelerad granskningsloggen

**Australien – 12 (1)** informationssystem kompilerar granskningsposter från [tilldelning: organisation definierats information systemkomponenter] i en systemomfattande (logisk eller fysisk) verifieringskedja som tid-korrelerad till inom [tilldelning: organisation definierats andelen tolerans för relationen mellan tidsstämplar för enskilda poster i granskningsloggen].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar Log Analytics-tjänsten i OMS. Log Analytics tillhandahåller övervakningstjänster för OMS genom att samla in data från hanterade resurser i en central databas. Granska poster tidsstämplar ändras inte, granskningsloggen är därför tid korrelerad. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-au-12-3"></a>NIST 800 53 kontrollen Australien – 12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Granska Generation | Ändringar av behöriga personer

**Australien – 12 (3)** informationssystem ger möjlighet till [tilldelning: organisation definierats enskilda användare eller roller] ändra granskningen kan utföras på [tilldelning: organisation definierats information systemkomponenter] baserat på [ Tilldelning: organisation definierats valbar händelsekriterier] i [tilldelning: organisation definierats tid tröskelvärden].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Åtkomst till granska funktioner begränsas med rollbaserad åtkomstkontroll i Azure och på den virtuella datorn OS-nivå. Konfigurationen av händelser som ska granskas av resurser som distribueras med den här modell kan konfigureras av användare med lämpliga rollbaserad auktorisering. |
| **Provider (Microsoft Azure)** | Inte tillämpligt |
