---
title: Azure-säkerhet och efterlevnad modell - FedRAMP Web Applications Automation - åtkomstkontroll
description: FedRAMP Web Applications Automation - åtkomstkontroll
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 3d30f889c4d7ed0dafcf8559e8987090c03f4d5e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207151"
---
# <a name="access-control-ac"></a>Åtkomstkontroll (nät)

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Referera till NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

## <a name="nist-800-53-control-ac-1"></a>NIST 800 53 kontrollen AC-1

#### <a name="access-control-policy-and-procedures"></a>Princip för åtkomstkontroll och procedurer

**AC-1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] en principer för åtkomstkontroll som åtgärdar syfte, scope, roller, ansvar, management åtagande, samordning bland organisationens entiteter och efterlevnad; och procedurer för att underlätta tillämpningen av principer för åtkomstkontroll och associerade åtkomstkontroller; och granskar och uppdaterar de aktuella principer för åtkomstkontroll [tilldelning: organisation definierats frekvens]; och åtkomst för kontroll [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens principer för åtkomstkontroll på företagsnivå och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2a"></a>NIST 800 53 kontrollen AC-2.a

#### <a name="account-management"></a>Kontohantering

**AC-2.a** organisationen identifierar och väljer sedan följande typer av information systemkonton att stödja organisationens uppdrag/företag funktion: [tilldelning: organisation definierats information system kontotyper].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet förlitar sig på och implementerar typer av följande system: Azure Active Directory-användare (används för att distribuera lösningen och hantera åtkomst till Azure-resurser), Windows OS-användare (hanteras av Active Directory), SQL Server-tjänstkontot. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2b"></a>NIST 800 53 kontrollen AC-2.b

#### <a name="account-management"></a>Kontohantering

**AC-2.b** organisationen tilldelar Kontoansvariga för systemkonton information.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att tilldela konton som identifierats i AC-02.a chefer. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2c"></a>NIST 800 53 kontrollen AC-2.c

#### <a name="account-management"></a>Kontohantering

**AC-2.c** organisationen upprättar villkor för medlemskap i gruppen och roll.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att upprätta roll och grupp kriterier för medlemskap för kund-kontrollerade kontotyper (se AC-02.a). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2d"></a>NIST 800 53 kontrollen AC-2.d

#### <a name="account-management"></a>Kontohantering

**AC-2.d** organisationen anger behöriga användare om informationssystem, grupp och roll medlemskap och åtkomst tillstånd (d.v.s. privilegier) och andra attribut (vid behov) för varje konto.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på en upprättad på företagsnivå konto auktoriseringsprocessen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2e"></a>NIST 800 53 kontrollen AC-2.e

#### <a name="account-management"></a>Kontohantering

**AC-2.e** Organisationen kräver godkännanden av [tilldelning: personal som definierats av organisationen eller roller] för begäranden om att skapa information Systemkonton.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på en upprättad på företagsnivå konto auktoriseringsprocessen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2f"></a>NIST 800 53 kontrollen AC-2.f

#### <a name="account-management"></a>Kontohantering

**AC-2.f** organisationen skapar, aktiverar, ändrar, inaktiverar och tar bort information systemkonton i enlighet med [tilldelning: organisation definierats procedurer eller villkor].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan vara beroende av en process för hantering av etablerade på företagsnivå konto. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2g"></a>NIST 800 53 kontrollen AC-2.g

#### <a name="account-management"></a>Kontohantering

**AC-2.g** organisationen övervakar användningen av information Systemkonton.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar säkerhet och gransknings-lösningens identitets- och instrumentpanel. Den här instrumentpanelen kan Kontoansvariga att övervaka användningen av information Systemkonton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2h"></a>NIST 800 53 kontrollen AC-2.h

#### <a name="account-management"></a>Kontohantering

**AC-2.h** organisationen meddelar Kontoansvariga när konton inte längre behövs, när användare avslutas eller överförs; och särskild information systemanvändning eller att behovsnivå ändringar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Procedurer för kundens företagsnivå åtkomstkontroll kan upprätta en process för att meddela chefen lämpligt konto när ett konto inte längre behövs. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2i"></a>NIST 800 53 kontrollen AC-2.i

#### <a name="account-management"></a>Kontohantering

**AC-2.i** organisationen tillåter åtkomst till det system baserat på en giltig åtkomstauktorisering; avsedda systemanvändning; och andra attribut som krävs av organisationen eller associerade uppdrag/funktionerna.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Procedurer för kundens företagsnivå åtkomstkontroll kan upprätta en åtkomst-auktoriseringsprocessen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2j"></a>NIST 800 53 kontrollen AC-2.j

#### <a name="account-management"></a>Kontohantering

**AC-2.j** organisationen granskar konton för kompatibilitet med kontot hanteringskrav [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för granska kund-kontrollerade konton för den obligatoriska frekvensen för att avgöra om konton som är kompatibla med alla organisationens krav. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-2k"></a>NIST 800 53 kontrollen AC-2.k

#### <a name="account-management"></a>Kontohantering

**AC-2.k** organisationen upprättar en process för att utfärda delade/grupp autentiseringsuppgifter (om distribuerad) när enskilda användare tas bort från gruppen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Procedurer för kundens företagsnivå åtkomstkontroll kan upprätta en process för hantering av grupp-kontouppgifter. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800 53 kontrollen AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Kontohantering | Automatisk hantering av systemkonto

**AC-2 (1)** organisationen använder automatisk mekanismer för hantering av information Systemkonton.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar säkerhet och gransknings-lösningens identitets- och instrumentpanel. Den här instrumentpanelen kan Kontoansvariga att övervaka användningen av information Systemkonton. Den kan konfigureras för att skicka aviseringar när ovanliga aktiviteten är misstänkt eller andra fördefinierade händelser inträffar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800 53 kontrollen AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Kontohantering | Borttagning av tillfälliga / nödfall konton

**AC-2 (2)** systemets information automatiskt [markeringen: tar bort; inaktiverar] tillfälliga och konton efter [tilldelning: definierad tidsperiod för varje typ av konto organisation].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar inte tillfälliga eller nödfall konton. Om du inte manuellt inaktiveras inaktiverar distribuerade domänkontrollanten automatiskt alla inaktiva konton efter 35 dagar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800 53 kontrollen AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Kontohantering | Inaktivera Inaktiva konton

**AC-2 (3)** information automatiskt inaktiveras Inaktiva konton efter [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Den domänkontrollant som distribueras med den här modell är konfigurerad för att inaktivera alla användarkonton efter 35 dagar av inaktivitet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800 53 kontrollen AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Kontohantering | Automatisk granskningsåtgärder

**AC-2 (4)** informationssystemet automatiskt granskningar konto skapas, ändras, aktivera, inaktivera eller borttagning åtgärder och meddelar, [tilldelning: personal som definierats av organisationen eller roller].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar typer av följande system: Azure Active Directory-användare, Windows OS-användare, SQL Server-tjänstkontot. Azure Active Directory-konto hanteringsåtgärder Generera en händelse i Azure-aktivitetsloggen; OS-nivå konto hanteringsåtgärder Generera en händelse i systemloggen. Dessa loggar samlas in av logganalys och lagras i logganalys-arbetsytan. Logganalys kan konfigureras för att skicka aviseringar när fördefinierade händelser inträffar.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800 53 kontrollen AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>Kontohantering | Inaktivitet logga ut

**AC-2 (5)** organisationen kräver att användare loggar ut när [tilldelning: organisation definierad tidsperiod på förväntade inaktivitet eller beskrivning av när du ska logga ut].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan upprätta en princip som användarna logga ut när de vill inaktiveras för en viss tidsperiod (eller andra faktorer). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800 53 styra AC-2 (7) schemanivån

#### <a name="account-management--role-based-schemes"></a>Kontohantering | Rollbaserad scheman

**AC-2.7 schemanivån** upprättar och tillämpar Privilegierade konton enligt ett schema för rollbaserad åtkomst som ordnar tillåtna information systemåtkomst och behörighet till roller.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar typer av följande system: Azure Active Directory-användare, Windows OS-användare, SQL Server-tjänstkontot. Azure Active Directory-tjänstkontot har implementerats med rollbaserad åtkomstkontroll genom att tilldela användare till roller; Behörighet för Active Directory implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till säkerhetsgrupper. Dessa rollbaserade scheman kan utökas av kunden uppdrag behov. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800 53 styra AC-2 (7) .b

#### <a name="account-management--role-based-schemes"></a>Kontohantering | Rollbaserad scheman

**AC-2.7 .b** organisationen övervakar Privilegierade rolltilldelningar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar säkerhet och granska lösningen identitets- och instrumentpanelen. Den här instrumentpanelen kan Kontoansvariga att övervaka användningen av information Systemkonton. Den här lösningen kan efterfrågas om du vill rapportera Privilegierade rolltilldelningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800 53 styra AC-2 (7) .c

#### <a name="account-management--role-based-schemes"></a>Kontohantering | Rollbaserad scheman

**AC-2.7 .c** organisationen tar [tilldelning: åtgärder som definierats av organisationen] när Privilegierade rolltilldelningar inte längre är lämpliga.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att vidta åtgärder för kund-kontrollerade konton när det inte längre att lämpliga Privilegierade rolltilldelningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800 53 kontrollen AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Kontohantering | Begränsningar för användning av delade / gruppen konton

**AC-2 (9)** organisationen tillåter endast att delade eller gruppkonton som uppfyller [tilldelning: organisation definierade villkor för att fastställa delade eller gruppkonton].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Inga delade/grupp-konton är aktiverade på resurser som distribueras med det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800 53 kontrollen AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Kontohantering | Delade / gruppen konto autentiseringsuppgifter uppsägning

**AC-2 (10)** informationssystem avslutar delade/grupp autentiseringsuppgifter när medlemmar lämna gruppen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Inga delade/grupp-konton är aktiverade på resurser som distribueras med det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800 53 kontrollen AC-2 (11)

#### <a name="account-management--usage-conditions"></a>Kontohantering | Villkor för användning

**AC-2 (11)** informationssystem tillämpar [tilldelning: organisation definierats omständigheter och/eller användning av villkor] för [tilldelning: organisation definierats information systemkonton].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En Grupprincip kan fastställas i Active Directory och konfigurerad för att implementera begränsningar för tid på dagen eller andra villkor för användning av kontot. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800 53 styra AC-2 (12) schemanivån

#### <a name="account-management--account-monitoring--atypical-usage"></a>Kontohantering | Kontot övervakning / ovanliga användning

**AC-2 (12) schemanivån** organisationen övervakar information systemkonton för [tilldelning: organisation definierats ovanliga användning].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar säkerhet och gransknings-lösningens identitets- och instrumentpanel. Den här instrumentpanelen kan Kontoansvariga att övervaka åtkomstförsök mot distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800 53 styra AC-2 (12) .b

#### <a name="account-management--account-monitoring--atypical-usage"></a>Kontohantering | Kontot övervakning / ovanliga användning

**AC-2 (12) .b** organisationen rapporterar ovanliga användning av information systemkonton till [tilldelning: personal som definierats av organisationen eller roller].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar säkerhet och gransknings-lösningens identitets- och instrumentpanel. Den här instrumentpanelen kan Kontoansvariga att övervaka åtkomstförsök mot distribuerade resurser. Den här lösningen kan konfigureras för att skicka aviseringar när ovanliga aktiviteten är misstänkt eller andra fördefinierade händelser inträffar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800 53 kontrollen AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Kontohantering | Inaktivera konton för hög risk enskilda användare

**AC-2 (13)** organisationen inaktiverar konton för de användare som utgör en betydande risk inom [tilldelning: definierad tidsperiod organisation] för identifiering av risk.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll och procedurer som kan upprätta villkor för att inaktivera konton för användare som utgör en betydande risk för organisationen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-3"></a>NIST 800 53 kontrollen AC-3

#### <a name="access-enforcement"></a>Tvingande Access

**AC-3** systemet tillämpar godkända tillstånd för logiska åtkomsten till information och systemresurser i enlighet med tillämpliga principer för åtkomstkontroll.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet tillämpar logisk åtkomst tillstånd med hjälp av rollbaserad åtkomstkontroll som tillämpas av Azure Active Directory genom att tilldela användare till roller, Active Directory genom att tilldela användare till säkerhetsgrupper, och kontrollerar Windows OS-nivå. Azure Active Directory-roller har tilldelats till användare eller grupper kontrollera logiska åtkomsten till resurser i Azure på nivån resurs, grupp eller prenumeration. Active Directory-säkerhetsgrupper åtkomstkontroll logiska till resurser för OS-nivå och funktioner. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-4"></a>NIST 800 53 kontrollen AC-4

#### <a name="information-flow-enforcement"></a>Tvingande för flödet av information

**AC-4** informationssystem tillämpar godkända tillstånd för att styra flödet av information i systemet och mellan sammanlänkade system baserat på [tilldelning: organisation definierats information principer för åtkomstkontroll av flödet].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet tillämpar information flödet begränsningar genom att använda nätverkssäkerhetsgrupper som tillämpas på undernät där resurser har distribuerats, Application Gateway och belastningsutjämnare. Nätverkssäkerhetsgrupper se till att kontrolleras informationsflödet mellan resurser baserat på godkända regler. Programmet Gateway och läsa in belastningsutjämning dynamiskt vidarebefordra trafik till specifika resurser baserat på godkända roller. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800 53 kontrollen AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Information flödet tvingande | Principen säkerhetsfilter

**AC-4 (8)** informationssystem tillämpar informationen flödet för en kontroll med hjälp av [tilldelning: organisation definierats princip säkerhetsfilter] som utgångspunkt för beslut om kontrollen flödet för [tilldelning: organisation definierats informationsflödet].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att genomdriva information flödeskontroll inom kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800 53 kontrollen AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Information flödet tvingande | Fysiska / logisk uppdelning av informationsflödet

**AC-4 (21)** systemet som avgränsar informationsflödet logiskt eller fysiskt använder [tilldelning: mekanismer som definierats av organisationen och/eller tekniker] att utföra [tilldelning: organisation definierats krävs separationer på typer av Information].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att avgränsa informationsflödet i kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-5a"></a>NIST 800 53 kontrollen AC-5.a

#### <a name="separation-of-duties"></a>Uppdelning av uppgifter

**AC-5.a** organisationen avgränsar [tilldelning: organisation definierats uppgifter för enskilda].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för uppdelning av uppgifter mellan kund-kontrollerade konton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-5b"></a>NIST 800 53 kontrollen AC-5.b

#### <a name="separation-of-duties"></a>Uppdelning av uppgifter

**AC-5.b** organisationen dokument uppdelning av uppgifter för enskilda användare.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att dokumentera uppdelning av uppgifter mellan kund-kontrollerade konton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-5c"></a>NIST 800 53 kontrollen AC-5.c

#### <a name="separation-of-duties"></a>Uppdelning av uppgifter

**AC-5.c** organisationen definierar information system åtkomst tillstånd att stödja uppdelning av uppgifter.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar rollbaserade åtkomstkontroller som kan konfigureras för att skilja uppgifter enligt organisationens krav. Azure Active Directory-tjänstkontot har implementerats med rollbaserad åtkomstkontroll genom att tilldela användare till roller; Behörighet för Active Directory implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till säkerhetsgrupper. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-6"></a>NIST 800 53 kontrollen AC-6

#### <a name="least-privilege"></a>Minsta behörighet

**AC-6** organisationen använder principen om minsta behörighet, så att endast auktoriserade åtkomst för användare (eller processer som agerar på uppdrag av användare) som är nödvändiga för att utföra aktiviteter i enlighet med organisationens uppdrag och funktioner för företag.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar rollbaserad åtkomstkontroll för att begränsa användare till endast de behörigheter som tilldelats. Azure Active Directory-tjänstkontot har implementerats med rollbaserad åtkomstkontroll genom att tilldela användare till roller; Behörighet för Active Directory implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till säkerhetsgrupper.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800 53 kontrollen AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Minsta behörighet | Bevilja åtkomst till säkerhetsfunktioner

**AC-6.1** organisationen uttryckligen tillåter åtkomst till [tilldelning: organisation definierats säkerhetsfunktioner (distribuerad av maskinvara, programvara och inbyggd programvara) och säkerhet information].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Procedurer för kundens företagsnivå åtkomstkontroll kan upprätta en åtkomst-auktoriseringen som ger åtkomst till säkerhetsfunktioner. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800 53 kontrollen AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Minsta behörighet | Icke-privilegierade åtkomst för icke-funktioner

**AC-6.2** organisationen kräver att användare av information systemkonton eller roller, med åtkomst till [tilldelning: organisation definierats säkerhetsfunktioner eller säkerhetsmässigt information], använda icke-privilegierade konton eller roller när åtkomst till icke-funktioner.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan måste användarna använda icke-privilegierade konton vid åtkomst till icke-funktioner. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800 53 kontrollen AC-6.3

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Minsta behörighet | Nätverksåtkomst till Privilegierade kommandon

**AC-6.3** organisationen auktoriserar nätverksåtkomst till [tilldelning: organisation definierats Privilegierade kommandon] endast för [tilldelning: organisation definierade intressant operativa behov] och dokument motiveringen för sådan åtkomst i den säkerhetsplan för systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan definiera Privilegierade kommandon som kan nås över ett nätverk. Obs: Kunder har ingen fysisk åtkomst till Azure-infrastrukturen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800 53 kontrollen AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Minsta behörighet | Privilegierade konton

**AC-6.5** organisationen begränsar Privilegierade konton i systemets information [tilldelning: personal som definierats av organisationen eller roller].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan definiera begränsningar för användningen av Privilegierade konton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800 53 styra AC-6 (7) schemanivån

#### <a name="least-privilege--review-of-user-privileges"></a>Minsta behörighet | Granskning av behörigheter

**AC-6 (7) schemanivån** organisation granskningar [tilldelning: organisation definierats frekvens] behörigheten för [tilldelning: organisation användardefinierade roller eller klasser av användare] Validera behovet av att sådan behörighet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska användarbehörigheter för kund-kontrollerade konton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800 53 styra AC-6 (7) .b

#### <a name="least-privilege--review-of-user-privileges"></a>Minsta behörighet | Granskning av behörigheter

**AC-6 (7) .b** organisationen tilldelar eller tar bort privilegier, om det behövs, korrekt återspeglar organisationens mål/företagsbehov.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att tilldela eller ta bort behörigheter för kund-kontrollerade konton när det är lämpligt. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800 53 kontrollen AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Minsta behörighet | Behörighetsnivåer för körning av kod

**AC-6 (8)** förhindrar att informationssystem [tilldelning: organisation definierats programvara] från som körs med högre behörighet än användare kör programmet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar rollbaserad åtkomstkontroll för att begränsa användare till endast de behörigheter som tilldelats. Skydd för virtuella OS-nivå tillåter inte att programvara ska köras på en högre Privilegienivå än användare kör programmet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800 53 kontrollen AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Minsta behörighet | Granska användning av Privilegierade funktioner

**AC-6 (9)** informationssystem granskningar körningen av Privilegierade funktioner.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar Log Analytics-tjänsten. Distribuera virtuella datorer och Azure diagnostics storage-konton är anslutna datakällor till logganalys att säkerställa att körningen av Privilegierade funktioner granskas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800 53 kontrollen AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Minsta behörighet | Förhindra att icke-privilegierade användare köra Privilegierade funktioner

**AC-6 (10)** systemet som förhindrar att icke-privilegierade användare körning Privilegierade funktioner för att inkludera inaktivera, kringgå, eller ändra implementerade säkerhet skydd/motåtgärder.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet implementerar rollbaserad åtkomstkontroll för att begränsa användare till endast de behörigheter som tilldelats.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-7a"></a>NIST 800 53 kontrollen AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>Misslyckade inloggningsförsök

**AC-7.a** informationssystem tillämpar en gräns på [tilldelning: organisation definierats nummer] på varandra följande felaktiga inloggningsförsök av en användare under en [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | I Azure portal gränser i följd felaktiga inloggningsförsök av användare. En Grupprincip tillämpas på operativsystemnivå för alla virtuella datorer som distribueras med det här utkastet. Principen begränsar på varandra följande felaktiga inloggningsförsök av användare till högst tre inom 15 minuter. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-7b"></a>NIST 800 53 kontrollen AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>Misslyckade inloggningsförsök

**AC-7.b** systemets information automatiskt [markeringen: låser konto/nod för en [tilldelning: definierad tidsperiod organisation]; låser konto/noden tills ut av en administratör; fördröjningar nästa inloggningsskärm enligt [ Tilldelning: organisation definierats fördröjning algoritmen]] när det maximala antalet misslyckade försök har överskridits.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Azure-portalen låser konton när efterföljande felaktiga inloggningsförsök av användare. En Grupprincip tillämpas på operativsystemnivå för alla virtuella datorer som distribueras med det här utkastet. Principen låser konton tre timmar efter tre på varandra följande felaktiga inloggningsförsök av användare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800 53 kontrollen AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Misslyckade inloggningsförsök | Ta bort / rensa mobila enheter

**AC-7 (2)** informationssystem återställningspunkter/rensning information från [tilldelning: organisation definierats mobila enheter] baserat på [tilldelning: organisation definierade Rensa/Rensa krav/teknik] efter [tilldelning: organisation definierats nummer] inloggningsförsök enhet på varandra följande, misslyckade.

**Ansvarsområden:** `Not Applicable`

|||
|---|---|
| **Kunden** | Mobila enheter inte omfattas av system distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte mobila enheter i Azure gränsen. Därför gäller inte den här kontrollen till Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-8a"></a>NIST 800 53 kontrollen AC-8.a

#### <a name="system-use-notification"></a>Använd Systemmeddelande

**AC-8.a** information visas för användare [tilldelning: organisation definierats systemet använder meddelande eller banderoll] innan åtkomst beviljas till system som ger konsekvent med tillämpliga federal meddelanden sekretess och säkerhet lagar Executive order, direktiven, principer, standarder och vägledning och tillstånd att användarna ansluter till en amerikansk Government informationssystem; vara kan övervakade information systemanvändning inspelade, och granskas; obehörig användning av informationssystem är förbjudna och omfattas straffrättsliga civil böter; och användning av informationssystem anger medgivande till övervakning och spela in.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En grupprincip implementerar en systemavisering för användning som visas för användarna innan inloggningen. Obs: Den här utkast implementerar en Använd Systemmeddelande exempel. Kunden måste redigera texten för att uppfylla organisation och/eller regulatoriska brödtext krav. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-8b"></a>NIST 800 53 kontrollen AC-8.b

#### <a name="system-use-notification"></a>Använd Systemmeddelande

**AC-8.b** informationssystem behåller meddelande eller banderollen på skärmen tills användare accepterar villkor för användning och åtgärda explicit logga in på eller ytterligare får åtkomst till systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En grupprincip implementerar en systemavisering för användning som visas för användarna innan inloggning. Användaren måste bekräfta meddelandet för att kunna logga in. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-8c"></a>NIST 800 53 kontrollen AC-8.c

#### <a name="system-use-notification"></a>Använd Systemmeddelande

**AC-8.c** Använd Systeminformation visas information för offentligt tillgänglig system [tilldelning: organisation definierade villkor], innan du beviljar åtkomst ytterligare; visar referenser, om sådant finns, övervakning, registrering, eller granskning som är konsekventa med sekretess logi för sådana system som förhindrar normalt dessa aktiviteter; och innehåller en beskrivning av den tillåtna användningen av systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att visa ett meddelande för användning av system för alla offentligt tillgänglig kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-10"></a>NIST 800 53 kontrollen AC-10

#### <a name="concurrent-session-control"></a>Samtidiga sessionskontroll

**AC-10** systemet som begränsar antalet samtidiga sessioner för varje [tilldelning: organisation definierats konto och/eller kontotyp] till [tilldelning: organisation definierats nummer].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | En princip för operativsystemet implementeras för virtuella datorer som distribueras med det här utkastet. Principen implementerar begränsningar för samtidiga sessionen (två sessioner). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-11a"></a>NIST 800 53 kontrollen AC-11.a

#### <a name="session-lock"></a>Sessionslås

**AC-11.a** systemet som förhindrar ytterligare tillgång till systemet genom att initiera en sessionslås efter [tilldelning: definierad tidsperiod organisation] inaktivitet eller när en begäran tas emot från en användare.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En grupprincip implementerar ett lås för inaktivitet för RDP-sessioner. Användare kan manuellt initiera låset. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-11b"></a>NIST 800 53 kontrollen AC-11.b

#### <a name="session-lock"></a>Sessionslås

**AC-11.b** informationssystem behåller sessionen låset tills användaren återupprättar åtkomst med hjälp av etablerade procedurer för identifiering och verifiering.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En grupprincip implementerar ett lås för inaktivitet för RDP-sessioner. Användarna måste autentiseras för att låsa upp sessionen.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800 53 kontrollen AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Sessionslås | Mönstret dölja visar

**AC-11 (1)** informationssystem döljer, via sessionslås information som tidigare synliga visas med en allmänheten avbildning.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En grupprincip implementerar ett lås för inaktivitet för RDP-sessioner. Sessionen låset innehåller information som tidigare visas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-12"></a>NIST 800 53 kontrollen AC-12

#### <a name="session-termination"></a>Sessionen avslutas

**AC-12** systemets information automatiskt avslutar en användarsession när [tilldelning: organisation definierade villkor eller utlösare händelser att koppla från sessionen].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Fjärrskrivbord värden sessionskonfigurationen för Windows-datorer som distribueras med den här modell kan konfigureras för att uppfylla krav för organisation sessionen avslutas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-12-1a"></a>NIST 800 53 kontrollen AC-12 (1) en

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Sessionen avslutas | Användarinitierad Logouts / Message visar

**AC-12.1 schemanivån** informationssystem ger en logga ut funktion för användarinitierad kommunikation sessioner när autentisering används för att få åtkomst till [tilldelning: organisation definierats informationsresurser].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Azure portal virtuella operativsystemen och distribueras med den här utkast aktivera använder för att initiera en logga ut. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-12-1b"></a>NIST 800 53 kontrollen AC-12 (1) .b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Sessionen avslutas | Användarinitierad Logouts / Message visar

**AC-12.1 .b** systemets information visar ett meddelande om explicit logga ut användare som anger att tillförlitliga kommunikationssessioner för autentiserad.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Azure portal virtuella operativsystemen och distribueras med den här utkast aktivera använder för att initiera en logga ut. Logga ut processen ger indikation för användarna att sessionen har avslutats. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-14a"></a>NIST 800 53 kontrollen AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Tillåtna åtgärder utan identifiering eller autentisering

**AC-14.a** organisationen identifierar [tilldelning: organisation definierats användaråtgärder] som kan utföras på systemet utan identifiering eller autentisering överensstämmer med organisationens uppdrag/funktionerna.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att identifiera åtgärder som kan utföras på kunden distribuerade resurser utan identifiering eller autentisering (t.ex. till exempel visar en offentligt tillgänglig webbsida). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-14b"></a>NIST 800 53 kontrollen AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Tillåtna åtgärder utan identifiering eller autentisering

**AC-14.b** organisationen dokument och innehåller stödjande motiveringen planen säkerhet för informationssystem, användaråtgärder som inte kräver identifiering eller autentisering.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att tillhandahålla dokumentation för användaråtgärder som inte kräver identifiering eller autentisering på kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-17a"></a>NIST 800 53 kontrollen AC-17.a

#### <a name="remote-access"></a>Fjärråtkomst

**AC-17.a** upprättar och dokument användningsbegränsningar, konfigurationsanslutning/krav och implementering vägledning för varje typ av fjärråtkomst som är tillåtna.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan definiera fjärråtkomst användningsbegränsningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-17b"></a>NIST 800 53 kontrollen AC-17.b

#### <a name="remote-access"></a>Fjärråtkomst

**AC-17.b** organisationen tillåter fjärråtkomst till informationssystemet innan dessa anslutningar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Procedurer för kundens företagsnivå åtkomstkontroll kan upprätta en fjärråtkomst auktoriseringsprocessen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800 53 kontrollen AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Fjärråtkomst | Automatisk övervakning / styra

**AC-17 (1)** systemet som övervakar och styr fjärråtkomstmetoder.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet ger fjärråtkomst till informationssystem via Azure-portalen via anslutning till fjärrskrivbord via en jumpbox och via en kund implementerade webbprogram. Åtkomst via Azure portal och fjärrskrivbordssessioner granskas och kan övervakas via logganalys. Kunden måste implementera fjärråtkomst kontroller som behövs för att webbprogrammet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800 53 kontrollen AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Fjärråtkomst | Skydd av sekretess / integritet med kryptering

**AC-17 (2)** informationssystem implementerar kryptografiska metoder för att skydda sekretess och integriteten för fjärråtkomst-sessioner.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Fjärråtkomst till resurser som distribueras med den här modell, inklusive Azure-portalen, fjärrskrivbord och web Programgateway skyddas med hjälp av TLS. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800 53 kontrollen AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Fjärråtkomst | Hanterad åtkomst kontrollpunkter

**AC-17 (3)** informationssystem dirigerar alla fjärranslutna åtkomst via [tilldelning: organisation definierats nummer] hanteras kontrollpunkter för network access.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Fjärråtkomst till konstruerade webbprogrammet distribueras med den här modell är via en Programgateway. Fjärråtkomst till alla andra resurser är via en jumpbox. Det finns inga offentliga slutpunkter. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800 53 styra AC-17 (4) schemanivån

#### <a name="remote-access--privileged-commands--access"></a>Fjärråtkomst | Privilegierad kommandon / åtkomst

**AC-17 (4) schemanivån** organisationen tillåter körning av Privilegierade kommandon och åtkomst till säkerhetsmässigt information via fjärråtkomst för endast [tilldelning: organisation definierade behov].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan definiera Privilegierade kommandon som kan nås via fjärranslutning och innehålla en motivering. Obs: Kunder har inga direkt tillgång till Azure-infrastrukturen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800 53 styra AC-17 (4) .b

#### <a name="remote-access--privileged-commands--access"></a>Fjärråtkomst | Privilegierad kommandon / åtkomst

**AC-17 (4) .b** organisationen dokument motiveringen för sådan åtkomst i skyddsplan för systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan definiera Privilegierade kommandon som kan nås via fjärranslutning och innehålla en motivering. Obs: Kunder har inga direkt tillgång till Azure-infrastrukturen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800 53 kontrollen AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Fjärråtkomst | Koppla från / inaktivera åtkomst

**AC-17 (9)** organisationen ger möjlighet att snabbt koppla från eller inaktivera fjärråtkomst till systemet inom [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet ger fjärråtkomst till informationssystemet via Azure portal, via anslutning till fjärrskrivbord via en jumpbox och via ett webbprogram. Om ett Azure Active Directory-konto har inaktiverats eller tagits bort, kopplas Azure portalåtkomst omedelbart. Om en virtuell dator OS-nivå kontot inaktiveras eller tas bort, kopplas på samma sätt fjärråtkomst till skrivbordet via jumpbox omedelbart. Kunderna måste implementera fjärråtkomst koppla för webbprogrammet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-18a"></a>NIST 800 53 kontrollen AC-18.a

#### <a name="wireless-access"></a>Trådlös åtkomst

**AC-18.a** organisationen upprättar användningsbegränsningar, konfigurationsanslutning/krav och implementering vägledning för trådlös åtkomst.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen trådlös åtkomst inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure upprättar användningsbegränsningar, konfigurationsanslutning/krav och implementering vägledning för trådlös åtkomst via den Network Security Standard, som uttryckligen förhindrar användning av trådlös i Microsoft Azure-miljön. |


 ## <a name="nist-800-53-control-ac-18b"></a>NIST 800 53 kontrollen AC-18.b

#### <a name="wireless-access"></a>Trådlös åtkomst

**AC-18.b** organisationen godkänner trådlös åtkomst till systemets information innan dessa anslutningar.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen trådlös åtkomst inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte trådlös åtkomst i Microsoft Azure-datacenter. |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800 53 kontrollen AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Trådlös åtkomst | Autentisering och kryptering

**AC-18 (1)** informationssystem skyddar trådlös åtkomst till systemet med autentisering av [markeringen (en eller flera): användare, enheter] och kryptering.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen trådlös åtkomst inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte trådlös åtkomst i Microsoft Azure-miljön. |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800 53 kontrollen AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Trådlös åtkomst | Inaktivera trådlösa nätverk

**AC-18 (3)** organisation inaktiverar, om inte avsett att användas, trådlösa nätverk internt bäddats information systemkomponenter innan utfärdande och distribution.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen trådlös åtkomst inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte trådlös åtkomst i Microsoft Azure-miljön. |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800 53 kontrollen AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Trådlös åtkomst | Begränsa konfigurationer av användare

**AC-18 (4)** identifierar och uttryckligen tillåter användare att konfigurera trådlösa nätverksfunktioner oberoende av varandra.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen trådlös åtkomst inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte trådlös åtkomst i Microsoft Azure-miljön. |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800 53 kontrollen AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Trådlös åtkomst | Antenner / överföring Power nivåer

**AC-18 (5)** organisationen väljer alternativknapp antenner och kalibrerar överföring energinivå för att minska sannolikheten för att använda signaler kan tas emot utanför organisationen-kontrollerade gränser.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen trådlös åtkomst inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte trådlös åtkomst i Microsoft Azure-miljön. |


 ## <a name="nist-800-53-control-ac-19a"></a>NIST 800 53 kontrollen AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Åtkomstkontroll för mobila enheter

**AC-19.a** organisationen upprättar användningsbegränsningar, konfiguration, anslutning krav och implementering vägledning för organisationen-kontrollerade mobila enheter.

**Ansvarsområden:** `Not Applicable`

|||
|---|---|
| **Kunden** | Det finns inga kund-kontrollerade mobila enheter inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte mobila enheter i Azure gränsen. Därför gäller inte den här kontrollen till Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19b"></a>NIST 800 53 kontrollen AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Åtkomstkontroll för mobila enheter

**AC-19.b** organisationen tillåter anslutning av mobila enheter till organisationsinformation system.

**Ansvarsområden:** `Not Applicable`

|||
|---|---|
| **Kunden** | Det finns inga kund-kontrollerade mobila enheter inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte mobila enheter i Azure gränsen. Därför gäller inte den här kontrollen till Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800 53 kontrollen AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Åtkomstkontroll för mobila enheter | Fullständig enhet / behållaren-baserad kryptering

**AC-19 (5)** organisationen använder [markeringen: fullständig enhetskryptering; behållaren kryptering] att skydda sekretess och integriteten hos information på [tilldelning: organisation definierats mobila enheter].

**Ansvarsområden:** `Not Applicable`

|||
|---|---|
| **Kunden** | Det finns inga kund-kontrollerade mobila enheter inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte mobila enheter i Azure gränsen. Därför gäller inte den här kontrollen till Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-20a"></a>NIST 800 53 kontrollen AC-20.a

#### <a name="use-of-external-information-systems"></a>Användning av externa informationssystem

**AC-20.a** organisationen upprättar villkoren, överensstämmer med alla förtroenderelationer med andra organisationer som äger, operativsystem, och/eller underhålla externa informationssystem, vilket gör att behöriga personer åtkomst till systemets information från extern information systems.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan innehålla en etablera om användningen av molnet Tjänsterbjudanden under FedRAMP. Azure har fått en provisorisk tillstånd att driva (P ATO) av den FedRAMP gemensamma auktorisering Board (JAB) gör att förvärv kan använda Azure-molntjänster av myndigheter. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-20b"></a>NIST 800 53 kontrollen AC-20.b

#### <a name="use-of-external-information-systems"></a>Användning av externa informationssystem

**AC-20.b** organisationen upprättar villkoren, överensstämmer med alla förtroenderelationer med andra organisationer som äger, operativsystem, och/eller underhålla externa informationssystem, vilket gör att behöriga personer Om du vill bearbeta, lagra eller överföra organisation-kontrollerade information med hjälp av externa informationssystem.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan innehålla en etablera om användningen av molnet Tjänsterbjudanden under FedRAMP. Azure har fått en provisorisk tillstånd att driva (P ATO) av den FedRAMP gemensamma auktorisering Board (JAB) gör att förvärv kan använda Azure-molntjänster av myndigheter. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800 53 kontrollen AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Användning av externa informationssystem | Gränserna för godkänd användning

**AC-20 (1)** organisationen tillåter behöriga individer att använda ett externt informationssystem att komma åt informationssystemet eller för att bearbeta, lagra och överföra organisation-kontrollerade information endast när organisationen verifierar den implementering av säkerhetsåtgärder som krävs i det externa systemet som anges i säkerhetsprincipen för organisationens information och säkerhetsplan. eller behåller godkända information system-anslutning eller bearbetning avtal med organisationens entitet som värd för det externa systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå information technology grupp kan kontrollera cloud service provider säkerhetskraven organisation information och godkänna företagsomfattande om du vill använda Tjänsterbjudanden associerade molnet. Azure har fått en provisorisk tillstånd att driva (P ATO) av den FedRAMP gemensamma auktorisering Board (JAB). Används för att utvärdera Azure av en FedRAMP godkända från tredje part assessment organisation (3PAO) för att kontrollera kompatibiliteten med styrning FedRAMP och andra krav. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800 53 kontrollen AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Användning av externa informationssystem | Flyttbara lagringsenheter

**AC-20 (2)** organisationen [markeringen: begränsar; förhindrar] användningen av organisation-kontrollerade flyttbara lagringsenheter av behöriga personer på extern informationssystem.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure-datacenter. |
| **Providern (Microsoft Azure)** | Microsoft kan inte kunden-kontrollerade flyttbara lagringsenheter i Microsoft Azure-miljön. |


 ## <a name="nist-800-53-control-ac-21a"></a>NIST 800 53 kontrollen AC-21.a

#### <a name="information-sharing"></a>Dela information

**AC-21.a** organisationen underlättar informationsdelning genom att aktivera behöriga användare att avgöra om åtkomst tillstånd som tilldelats till den dela partnern matchar åtkomstbegränsningarna för information om [tilldelning: organisation definierats informationsdelning användaren gottfinnande krävs].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå principer för åtkomstkontroll kan innehålla bestämmelser om att dela information. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-21b"></a>NIST 800 53 kontrollen AC-21.b

#### <a name="information-sharing"></a>Dela information

**AC-21.b** organisationen använder [tilldelning: organisation definierats automatiserade metoder eller manuella processer] att hjälpa användarna beslutsfattande information delning och samarbete.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på företagsnivå information delning beslut stöd för funktionen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-22a"></a>NIST 800 53 kontrollen AC-22.a

#### <a name="publicly-accessible-content"></a>Offentligt tillgänglig innehåll

**AC-22.a** organisationen anger enskilda användare behörighet att publicera information till ett offentligt tillgänglig informationssystem.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Procedurer för kundens företagsnivå åtkomstkontroll kan ange de personer som har behörighet att publicera offentligt tillgänglig information. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-22b"></a>NIST 800 53 kontrollen AC-22.b

#### <a name="publicly-accessible-content"></a>Offentligt tillgänglig innehåll

**AC-22.b** organisationen tränar behöriga individer för att säkerställa att offentligt tillgänglig information inte innehåller icke allmän information.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på företagsnivå utbildning för enskilda användare behörighet att publicera offentligt tillgänglig information. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-22c"></a>NIST 800 53 kontrollen AC-22.c

#### <a name="publicly-accessible-content"></a>Offentligt tillgänglig innehåll

**AC-22.c** organisationen granskar information före bokföring till systemets offentligt tillgänglig information för att säkerställa att icke allmän information inte ingår föreslagna innehållet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Procedurer för kundens företagsnivå åtkomstkontroll kan upprätta en granskningsprocess för innehåll som föreslås ska publiceras en offentligt tillgänglig system. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ac-22d"></a>NIST 800 53 kontrollen AC-22.d

#### <a name="publicly-accessible-content"></a>Offentligt tillgänglig innehåll

**AC-22.d** organisationen har granskat innehållet på systemets offentligt tillgänglig information för icke allmän information [tilldelning: organisation definierats frekvens] och tar bort sådan information om identifierade.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Procedurer för kundens företagsnivå åtkomstkontroll kan upprätta en process för regelbunden granskning av innehåll i offentligt tillgänglig system. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |

