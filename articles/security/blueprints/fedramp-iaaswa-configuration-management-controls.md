---
title: Azure-säkerhet och hantering av regelefterlevnad modell - FedRAMP Web Applications Automation - konfiguration
description: FedRAMP Web Applications Automation - konfigurationshantering
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 356eccac6af2780c02d1cd935d41891b5f89f1a2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="configuration-management-cm"></a>Konfigurationshantering (CM)

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Läs NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

## <a name="nist-800-53-control-cm-1"></a>NIST 800 53 kontrollen CM-1

#### <a name="configuration-management-policy-and-procedures"></a>Princip för hantering av konfiguration och procedurer

**CM-1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] en princip för hantering av konfiguration som åtgärdar syfte, scope, roller, ansvar, management åtagande samordning av organisationens entiteter och efterlevnad; och procedurer för att underlätta genomförandet av princip för hantering av konfiguration och associerade configuration management-kontroller. och granskar och uppdaterar den aktuella konfiguration hanteringsprincipen [tilldelning: organisation definierats frekvens]; och procedurer som configuration management [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens princip för hantering av företagsnivå konfiguration och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-2"></a>NIST 800 53 kontrollen CM-2

#### <a name="baseline-configuration"></a>Grundläggande konfiguration

**CM-2** organisation utvecklar, dokument och underhåller under configuration kontroll, en aktuell grundläggande konfiguration av den.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Representerar en ”som kod” konfigurationsbaslinje för distribuerad arkitektur Azure Resource Manager-mallar och tillhörande resurser som utgör det här utkastet. Lösningen tillhandahålls via GitHub, som kan användas för av konfigurationskontrollen. Lösningen innehåller en baslinje för önskad tillstånd Configuration (DSC) för varje distribuerad virtuell dator. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-2-1a"></a>NIST 800 53 kontrollen CM-2 (1) en

#### <a name="baseline-configuration--reviews-and-updates"></a>Grundläggande konfiguration | Granskning och uppdateringar

**CM-2 (1) en** organisationen granskar och uppdaterar grundläggande konfiguration av informationssystem [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska och uppdatera grundläggande konfiguration av kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-2-1b"></a>NIST 800 53 kontrollen CM-2 (1) .b

#### <a name="baseline-configuration--reviews-and-updates"></a>Grundläggande konfiguration | Granskning och uppdateringar

**CM-2 (1) .b** organisationen granskar och uppdaterar grundläggande konfiguration av den vid behov på grund av [tilldelningen organisation definierats omständigheter].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska och uppdatera grundläggande konfiguration av kunden distribuerade resurser vid behov. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-2-1c"></a>NIST 800 53 kontrollen CM-2 (1) .c

#### <a name="baseline-configuration--reviews-and-updates"></a>Grundläggande konfiguration | Granskning och uppdateringar

**CM-2 (1) .c** organisationen granskar och uppdaterar grundläggande konfiguration av den som en del av information system komponentinstallationer och uppgraderingar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska och uppdatera grundläggande konfiguration av kunden distribuerade resurser vid behov. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800 53 kontrollen CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Grundläggande konfiguration | Stöd för automatisering för Precision / valuta

**CM-2 (2)** organisationen använder automatisk mekanismer för att underhålla en aktuell, fullständig, tillförlitlig och tillgänglig grundläggande konfiguration av den.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Representerar en ”som kod” konfigurationsbaslinje för distribuerad arkitektur Azure Resource Manager-mallar och tillhörande resurser som utgör det här utkastet. Lösningen tillhandahålls via GitHub, som kan användas för av konfigurationskontrollen. Ett automation-skript är tillgänglig för alla distribuerade resurser i Azure-portalen och ger en alltid uppdaterad representation av dessa resurser.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800 53 kontrollen CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Grundläggande konfiguration | Kvarhållning av tidigare konfigurationer

**CM-2 (3)** organisationen behåller [tilldelning: organisation definierats tidigare versioner av baslinjen konfigurationer av informationssystem] för återställningsfunktionen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att behålla tidigare versioner av baslinjen konfigurationer för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800 53 styra schemanivån för CM-2 (7)

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Grundläggande konfiguration | Konfigurera system, komponenter och enheter för hög risk områden

**CM-2.7 schemanivån** organisation problem [tilldelning: organisation definierats informationssystem, systemkomponenter eller enheter] med [tilldelning: konfigurationer som definierats av organisationen] till personer som reser till platser som organisationen anser vara av betydande risk.

**Ansvarsområden:** `Not Applicable`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade fysiska enheter inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure kunden innehållet lagras aldrig utanför Microsoft Azure, vilket inte fysiskt finns inom continental USA. Microsoft Azure-personal reser inte med enheter som ingår i Microsoft Azure-lagret. Därför gäller inte den här kontrollen till Microsoft Azure. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800 53 styra CM-2 (7) .b

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Grundläggande konfiguration | Konfigurera system, komponenter och enheter för hög risk områden

**CM-2.7 .b** organisationen tillämpar [tilldelning: organisation definierats säkerhetsåtgärder] till enheter när enskilda tillbaka.

**Ansvarsområden:** `Not Applicable`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade fysiska enheter inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure kunden innehåll lagras aldrig utanför Microsoft Azure och Microsoft Azure-personal reser inte med enheter som ingår i Microsoft Azure-lagret, vilket denna kontroll gäller inte. |


 ## <a name="nist-800-53-control-cm-3a"></a>NIST 800 53 kontrollen CM-3.a

#### <a name="configuration-change-control"></a>Konfiguration för ändringshantering

**CM-3.a** organisationen bestämmer vilka typer av ändringar i systemet som styrs av konfiguration.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att bestämma vilka typer av ändringar av kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara) är styrs av konfiguration. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-3b"></a>NIST 800 53 kontrollen CM-3.b

#### <a name="configuration-change-control"></a>Konfiguration för ändringshantering

**CM-3.b** organisationen granskar föreslagna configuration-kontrollerade ändringar i systemet och godkänner eller disapproves ändringarna med explicit ersättningen för säkerhet påverkan analys.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska föreslagna configuration-kontrollerade ändringar till kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-3c"></a>NIST 800 53 kontrollen CM-3.c

#### <a name="configuration-change-control"></a>Konfiguration för ändringshantering

**CM-3.c** dokument organisationskonfiguration ändrar de beslut som är associerade med systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att dokumentera configuration-kontrollerade ändringar som är kopplade till kunden distribuerade resurser (se 03.b CM). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-3d"></a>NIST 800 53 kontrollen CM-3.d

#### <a name="configuration-change-control"></a>Konfiguration för ändringshantering

**CM-3.d** organisationen implementerar godkända configuration-kontrollerade ändringar i systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att implementera configuration-kontrollerade ändringar som godkänts i CM-03.b. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-3e"></a>NIST 800 53 kontrollen CM-3.e

#### <a name="configuration-change-control"></a>Konfiguration för ändringshantering

**CM-3.e** Organisationen behåller poster om configuration-kontrollerade ändringar i systemet för [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att behålla en post av configuration-kontrollerade ändringar av kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-3f"></a>NIST 800 53 kontrollen CM-3.f

#### <a name="configuration-change-control"></a>Konfiguration för ändringshantering

**CM-3.f** granskningar och granskar aktiviteter som är associerade med systemet configuration-kontrollerade ändringarna.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för granskning och granskning av ändringar i konfigurationen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-3g"></a>NIST 800 53 kontrollen CM-3.g

#### <a name="configuration-change-control"></a>Konfiguration för ändringshantering

**CM-3.g** organisationen samordnar och ger tillsyn för konfigurationsaktiviteter Ändra kontroll via [tilldelning: organisation definierats Ändra kontroll konfigurationselement (t.ex. kommittén, board)] som sammanträder till [markeringen ( en eller flera): [tilldelning: organisation definierats frekvens]; [Tilldelning: organisation definierats configuration ändra villkor]].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att samordna och tillhandahålla tillsyn för konfigurationsaktiviteter Ändra kontroll. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-1a"></a>NIST 800 53 kontrollen CM-3 (1) en

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfiguration för ändringshantering | Automatisk dokumentet / meddelanden / förbud mot ändringar

**CM-3 (1) en** organisationen använder automatisk mekanismer för att dokumentera föreslagna ändringar i systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda automatisk mekanismer för att dokumentera föreslagna ändringar (se 03.b CM). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-1b"></a>NIST 800 53 kontrollen CM-3 (1) .b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfiguration för ändringshantering | Automatisk dokumentet / meddelanden / förbud mot ändringar

**CM-3 (1) .b** organisationen använder automatisk mekanismer för att meddela [tilldelning: ordnade definierats godkännandemyndigheter] av föreslagna ändringar av informationen om systemet och begäran ändra godkännande.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda en automatiserad mekanism för vägen och begäran om godkännande för föreslagna ändringar till kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-1c"></a>NIST 800 53 kontrollen CM-3 (1) .c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfiguration för ändringshantering | Automatisk dokumentet / meddelanden / förbud mot ändringar

**CM-3 (1) .c** organisationen använder automatisk mekanismer för att visa föreslagna ändringar till det system som inte godkänts eller underkänts av [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda en automatiserad metod för att markera inte granskats ändra förslag. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-1d"></a>NIST 800 53 kontrollen CM-3 (1) .d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfiguration för ändringshantering | Automatisk dokumentet / meddelanden / förbud mot ändringar

**CM-3 (1) .d** organisationen använder automatisk mekanismer för att förhindra ändringar i systemet tills avsedda godkännanden tas emot.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda en automatiserad mekanism för att förhindra implementeringen av icke-godkända ändringar av kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-1e"></a>NIST 800 53 kontrollen CM-3 (1) .e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfiguration för ändringshantering | Automatisk dokumentet / meddelanden / förbud mot ändringar

**CM-3 (1) .e** Organisationen använder automatisk mekanismer för att dokumentera alla ändringar till systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda en automatiserad metod för att dokumentera alla implementerade ändringar av kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-1f"></a>NIST 800 53 kontrollen CM-3 (1) .f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfiguration för ändringshantering | Automatisk dokumentet / meddelanden / förbud mot ändringar

**CM-3 (1) .f** organisationen använder automatisk mekanismer för att meddela [tilldelning: organisation definierats personal] när godkända ändringar i systemet är slutförda.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda en automatiserad mekanism för meddelanden när godkända ändringar till kunden distribuerade resurser har slutförts. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800 53 kontrollen CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>Konfiguration för ändringshantering | Testa / Validera / dokumentera ändringar

**CM-3 (2)** organisationen testar validerar och dokument ändringar i systemets information innan du implementerar ändringarna i operativa systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för testning, och dokumentera ändringar till kunden distribuerade resurser före implementeringen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800 53 kontrollen CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>Konfiguration för ändringshantering | Säkerhet ombud

**CM-3 (4)** organisationen kräver en information security representant om du vill vara medlem i den [tilldelning: organisation definierats Ändra kontroll konfigurationselementet].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att tilldela en information security representant om du vill vara medlem i det ändringen kontrollen elementet har definierats i CM-03.g. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800 53 kontrollen CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>Konfiguration för ändringshantering | Hantering av kryptografi

**CM-3 (6)** organisationen garanterar att kryptografiska metoder används för att tillhandahålla [tilldelning: organisation definierats säkerhetsåtgärder] under konfigurationshantering.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att säkerställa att kryptografiska metoder är under konfigurationshantering. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-4"></a>NIST 800 53 kontrollen CM-4

#### <a name="security-impact-analysis"></a>Konsekvensanalys för säkerhet

**CM-4** organisationen analyserar ändringar i systemet för att identifiera potentiella påverkan på säkerhet innan ändringen genomförs.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att analysera föreslagna ändringar till kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800 53 kontrollen CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>Säkerhet konsekvensanalys | Separata testmiljöer

**CM-4.1** organisationen analyserar ändringar i systemet i en separat testmiljö före implementering i en driftsmiljö, söker efter säkerhet påverkan på grund av fel, svagheter och inkompatibilitet eller avsiktligt malice.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att analysera föreslagna ändringar till kunden distribuerade resurser i en testmiljö före implementering i en driftsmiljö. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-5"></a>NIST 800 53 kontrollen CM-5

#### <a name="access-restrictions-for-change"></a>Åtkomstbegränsningar för ändringen

**CM-5** organisationen definierar, dokument, godkänner och tillämpar fysiska och logiska åtkomstbegränsningar som är associerade med ändringar i systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Azure Active Directory kontot behörighet implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till roller som tillhandahåller strikt kontroll över vilka användare kan visa och kontroll distribuerade resurser. Behörighet för Active Directory implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till säkerhetsgrupper. Dessa säkerhetsgrupper styr åtgärder som användarna kan utföra med avseende på operativsystemets konfiguration. Dessa rollbaserade scheman kan utökas av kunden uppdrag behov. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800 53 kontrollen CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Åtkomst till begränsningar för ändringen | Automatisk åtkomst tvingande / granskning

**CM-5 (1)** systemet som tillämpar åtkomstbegränsningar och stöder granskning av tvingande åtgärder.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Azure Active Directory kontot behörighet implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till roller som tillhandahåller strikt kontroll över vilka användare kan visa och kontroll distribuerade resurser. Behörighet för Active Directory implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till säkerhetsgrupper. Dessa säkerhetsgrupper styr åtgärder som användarna kan utföra med avseende på operativsystemets konfiguration. Alla har åtkomst och åtkomstförsök granskas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800 53 kontrollen CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>Åtkomst till begränsningar för ändringen | Granska systemändringar

**CM-5.2** organisationen granskar information systemändringar [tilldelning: organisation definierats frekvens] och [tilldelning: organisation definierats omständigheter] att fastställa om obehöriga ändringar har inträffat.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska ändringar av kunden distribuerade resurser för att fastställa om obehöriga ändringar har inträffat. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800 53 kontrollen CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>Åtkomst till begränsningar för ändringen | Signerade komponenter

**CM-5.3** systemet som hindrar installationen av [tilldelning: organisation definierats programvara och inbyggd programvara komponenter] utan att verifiera att komponenten har signerats digitalt använder ett certifikat som identifieras och godkänd av organisationen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Virtuella datorer som distribueras med den här utkast implementera Windows AppLocker för att ange vilka användare kan installera eller köra vissa program. Ytterligare, alla Windows uppdateringar av operativsystemet är digitalt singed. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800 53 styra schemanivån för CM-5 (5)

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Åtkomst till begränsningar för ändringen | Begränsa produktion / Operational privilegier

**CM-5 (5) schemanivån** organisationen begränsar behörigheter om du vill ändra information systemkomponenter och system-relaterad information i en produktionsmiljö eller driftsmiljön.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att begränsa behörighet för att göra ändringar i kunden distribuerade produktion eller operativa miljöer. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800 53 styra CM-5 (5) .b

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Åtkomst till begränsningar för ändringen | Begränsa produktion / Operational privilegier

**CM-5 (5) .b** organisationen granskar och reevaluates privilegier [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska och reevaluating privilegier som definieras i en CM-05 (05). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-6a"></a>NIST 800 53 kontrollen CM-6.a

#### <a name="configuration-settings"></a>Konfigurationsinställningar

**CM-6.a** upprättar och dokument konfigurationsinställningar för information som används inom information system med [tilldelning: organisation definierats security configuration checklistor] som motsvarar Det mest restriktiva läget är konsekventa med operativa krav.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet innehåller en baslinje för önskad tillstånd Configuration (DSC) för varje distribuerad virtuell dator. Dessa deklarativ PowerShell-skript definierar och konfigurerar de resurser som de tillämpas. Baslinjen DSC ingår för resurser som distribueras med den här lösningen kan utökas av kunden uppdrag behov. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-6b"></a>NIST 800 53 kontroll-CM-6 b

#### <a name="configuration-settings"></a>Konfigurationsinställningar

**CM-6 b** organisationen implementerar konfigurationsinställningarna.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet innehåller en baslinje för önskad tillstånd Configuration (DSC) för varje distribuerad virtuell dator. Baslinjer automatiskt tillämpas på virtuella datorer under distributionen med hjälp av tillägget för anpassat skript för virtuell dator. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-6c"></a>NIST 800 53 kontrollen CM-6

#### <a name="configuration-settings"></a>Konfigurationsinställningar

**CM-6** organisationen identifierar, dokument och godkänner eventuella avvikelser från etablerade konfigurationsinställningar för [tilldelning: organisation definierats information systemkomponenter] baserat på [tilldelning: organisation definierats operativa krav].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att identifiera, dokumentera och godkänna eventuella avvikelser från etablerade konfigurationsinställningar för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-6d"></a>NIST 800 53 kontrollen CM-6.d

#### <a name="configuration-settings"></a>Konfigurationsinställningar

**CM-6.d** organisation Övervakare och kontroller ändras till konfigurationsinställningarna i enlighet med organisationens principer och procedurer.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Automation DSC. Automation DSC justerar datorkonfigurationer med en viss konfiguration som definierats av organisationen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800 53 kontrollen CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Konfigurationsinställningar för | Automatiserad Central hantering / Application / verifiering

**CM-6.1** organisationen använder automatisk mekanismer för att centralt hantera, tillämpa och verifiera konfigurationsinställningarna för [tilldelning: organisation definierats information systemkomponenter].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Azure Automation DSC. Automation DSC justerar datorkonfigurationer med en viss konfiguration som definierats av organisationen och övervakar kontinuerligt för ändringar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800 53 kontrollen CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Konfigurationsinställningar för | Svara på obehöriga ändringar

**CM-6.2** organisationen använder [tilldelning: organisation definierats säkerhetsåtgärder] ska svara på obehöriga ändringar av [tilldelning: organisation definierats konfigurationsinställningar].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Azure Automation DSC. Automation DSC kan konfigureras att generera en avisering eller för att åtgärda felaktiga konfigurationer om identifieras. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-7a"></a>NIST 800 53 kontrollen CM-7.a

#### <a name="least-functionality"></a>Minst funktioner

**CM-7.a** organisationen konfigurerar systemet för att tillhandahålla endast grundläggande funktioner.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Resurser som har distribuerats av det här utkastet konfigureras för att tillhandahålla minst funktioner för deras syfte. En önskad tillstånd Configuration DSC ()-baslinje ingår för varje distribuerad virtuell dator. Dessa deklarativ PowerShell-skript definierar och konfigurerar de resurser som de tillämpas. Baslinjen DSC ingår för resurser som distribueras med den här lösningen kan utökas av kunden att ytterligare begränsa funktioner som uppfyller behoven för verksamhetskritiska. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-7b"></a>NIST 800 53 kontrollen CM-7.b

#### <a name="least-functionality"></a>Minst funktioner

**CM-7.b** organisationen hindrar eller begränsar användningen av de följande funktioner, portar, protokoll och tjänster: [tilldelning: organisation användardefinierade förbjudna eller begränsade funktioner, portar, protokoll och tjänster].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Azure Application Gateway och nätverk säkerhetsgrupper för att begränsa användningen av portar och protokoll till endast de som är nödvändiga. Programgateway nätverkssäkerhetsgrupper och DSC baslinjer för virtuella datorer kan ytterligare konfigureras av kunden att begränsa användningen av funktioner, portar, protokoll och tjänster för att tillhandahålla de funktioner som avsett. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-7-1a"></a>NIST 800 53 kontrollen CM-7 (1) en

#### <a name="least-functionality--periodic-review"></a>Minst funktioner | Regelbunden granskning

**CM-7.1 schemanivån** organisationen granskar informationssystem [tilldelning: organisation definierats frekvens] att identifiera onödiga och/eller skyddade funktioner, portar, protokoll och tjänster.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska kunden distribuerade resurser (om du vill inkludera program, operativsystem, databaser och programvara) för att identifiera onödiga och/eller osäkra funktioner, portar, protokoll och tjänster. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-7-1b"></a>NIST 800 53 kontrollen CM-7 (1) .b

#### <a name="least-functionality--periodic-review"></a>Minst funktioner | Regelbunden granskning

**CM-7.1 .b** organisation inaktiverar [tilldelning: organisation användardefinierade funktioner, portar, protokoll och tjänster inom det system anses vara onödiga och/eller skyddade].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att inaktivera funktioner, portar, protokoll och tjänster som har bedömts vara onödiga eller osäkra. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800 53 kontrollen CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>Minst funktioner | Förhindra att programmet körs

**CM-7.2** informationssystem förhindrar programkörningen i enlighet med [markeringen (en eller flera): [tilldelning: organisation definierade principer om programvara som programmet användning och begränsningar]; regler för auktorisering av villkoren och villkor för användning av programvara program].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att förhindra att programmet körs i enlighet med kunddefinierade programvara användningsprinciper för programmet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800 53 styra schemanivån för CM-7 (5)

#### <a name="least-functionality--authorized-software--whitelisting"></a>Minst funktioner | Godkända programvara / Vitlistning

**CM-7.5 schemanivån** organisationen identifierar [tilldelning: organisation definierats program behörighet för att köra i systemet].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att identifiera auktoriserade program. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800 53 styra CM-7 (5) .b

#### <a name="least-functionality--authorized-software--whitelisting"></a>Minst funktioner | Godkända programvara / Vitlistning

**CM-7.5 .b** organisationen använder en neka alla tillstånd av undantag principen så att körningen av auktoriserade program på informationssystemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda en neka alla tillstånd av undantag principen så att körningen av auktoriserade program på kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800 53 styra CM-7 (5) .c

#### <a name="least-functionality--authorized-software--whitelisting"></a>Minst funktioner | Godkända programvara / Vitlistning

**CM-7.5 .c** organisationen granskar och uppdaterar listan över auktoriserade program [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att granska och uppdatera listan över godkända programvaror. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-8a"></a>NIST 800 53 kontrollen CM-8.a

#### <a name="information-system-component-inventory"></a>Information komponenten Systeminformation

**CM-8.a** organisation utvecklar och dokumenterar en förteckning över information systemkomponenter som visar det aktuella systemet; inkluderar alla komponenter inom gränserna för auktorisering av informationssystem; är i korrekt önskad nivå bedöms vara nödvändiga för spårning och rapportering. samt [tilldelning: organisation definierats information anser nödvändiga för att uppnå effektiva system komponenten accountability].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar alla resurser till en resursgrupp i Azure Resource Manager. Azure Resource Manager tillhandahåller en alltid uppdaterad lista över distribuerade resurser och kan anpassas till taggen och grupp resurser för hantering av inventering. Resurser som distribueras med den här lösningen får en viss resurs-tagg som kan associeras med systemgränsen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-8b"></a>NIST 800 53 kontrollen CM-8.b

#### <a name="information-system-component-inventory"></a>Information komponenten Systeminformation

**CM-8.b** organisationen granskar och uppdaterar komponenten Systeminformation information [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar alla resurser till en resursgrupp i Azure Resource Manager. Azure Resource Manager tillhandahåller en alltid uppdaterad lista över distribuerade resurser som är tillgänglig för granskning i Azure-portalen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800 53 kontrollen CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Information komponenten Systeminformation | Uppdateringar under installationer / borttagningar

**CM-8.1** organisationen uppdaterar lagret information systemkomponenter som en del av komponentinstallationer, borttagning och uppdatering av systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar alla resurser till en resursgrupp i Azure Resource Manager. Bladet resurser i Azure-portalen visar en lista över alla distribuerade resurser, tillhandahålla en alltid uppdaterad inventering som resurser har distribuerats och tas bort. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800 53 kontrollen CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>Information komponenten Systeminformation | Automatiserat underhåll

**CM-8 (2)** organisationen använder automatisk mekanismer för att upprätthålla en aktuell, fullständig, tillförlitlig och tillgänglig förteckning över information systemkomponenter.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar alla resurser till en resursgrupp i Azure Resource Manager. Bladet resurser i Azure-portalen visar en lista över alla distribuerade resurser, tillhandahålla en alltid uppdaterad inventering som resurser har distribuerats och tas bort. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800 53 styra CM-8 (3) en

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Information komponenten Systeminformation | Automatisk identifiering av obehöriga komponent

**CM-8.3 schemanivån** organisationen använder automatisk mekanismer [tilldelning: organisation definierats frekvens] att upptäcka otillåten maskinvara, programvara och inbyggd programvara komponenter i informationssystemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda automatisk mekanismer för att detektera förekomst av otillåten programvara i kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800 53 styra CM-8 (3) .b

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Information komponenten Systeminformation | Automatisk identifiering av obehöriga komponent

**CM-8.3 .b** organisationen utförs följande åtgärder när obehörig komponenter identifieras: [markeringen (en eller flera): inaktiverar nätverksåtkomst av dessa komponenter, isolerar komponenterna, meddelar [tilldelning: organisation definierats personal eller roller]].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att vidta åtgärder när otillåten programvara upptäcks. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800 53 kontrollen CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>Information komponenten Systeminformation | Accountability Information

**CM-8 (4)** organisationen innehåller i information system komponenten inventeringsinformation, ett sätt för att identifiera av [markeringen (en eller flera): namn, position, roll], individer ansvarig/ansvariga för administration av dessa komponenter .

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar alla resurser till en resursgrupp i Azure Resource Manager. Azure-resurs-taggar är nyckel / värde-par som kan användas för att kategorisera resurser för accountability och/eller management ändamål. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800 53 kontrollen CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Information komponenten Systeminformation | Inga dubblerade redovisning komponenter

**CM-8.5** organisationen verifierar att alla komponenter inom gränserna för auktorisering av informationssystem inte dupliceras i andra information system komponenten lager.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar alla resurser till en resursgrupp i Azure Resource Manager. Azure Resource Manager tillhandahåller en alltid uppdaterad lista över distribuerade resurser. Resurser som distribueras med den här lösningen får en viss resurs-tagg som kan associeras med systemgränsen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-9a"></a>NIST 800 53 kontrollen CM-9.a

#### <a name="configuration-management-plan"></a>Plan för konfiguration

**CM-9.a** organisation utvecklar, dokument och implementerar en konfiguration av en plan för det system som åtgärdar roller, ansvar, och configuration management processer och procedurer.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utveckla, dokumentera och genomföra en plan för hantering av konfiguration för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-9b"></a>NIST 800 53 kontrollen CM-9.b

#### <a name="configuration-management-plan"></a>Plan för konfiguration

**CM-9.b** organisation utvecklar, dokument och implementerar en plan för konfigurationen för det system som upprättar en process för att växla identifierande konfigurationsobjekt under utveckling hela systemet samt för Hantera konfigurationen av konfigurationsobjekt.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utveckla, dokumentera och genomföra en plan för hantering av konfiguration för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-9c"></a>NIST 800 53 kontrollen CM-9.c

#### <a name="configuration-management-plan"></a>Plan för konfiguration

**CM-9.c** organisation utvecklar, dokument och implementerar en plan för hantering av konfigurationen för det system som definierar konfigurationen för systemet och placerar konfigurationsobjekt under konfiguration hantering.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utveckla, dokumentera och genomföra en plan för hantering av konfiguration för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-9d"></a>NIST 800 53 kontrollen CM-9.d

#### <a name="configuration-management-plan"></a>Plan för konfiguration

**CM-9.d** organisation utvecklar, dokument och implementerar en plan för konfigurationen för det system som skyddar planen för hantering av konfiguration från obehöriga avslöjande och ändringar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att utveckla, dokumentera och genomföra en plan för hantering av konfiguration för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-10a"></a>NIST 800 53 kontrollen CM-10.a

#### <a name="software-usage-restrictions"></a>Programvara användningsbegränsningar

**CM-10.a** organisationen använder programvaran och tillhörande dokumentation i enlighet med avtalet avtal och upphovsrättslagstiftning.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Windows- och SQL Server-licenser ingår för resurser som har distribuerats av det här utkastet. Detta är en inbyggd funktion i Azure. Organisationer med befintliga licensavtal kan överväga att distribuera alternativa licens modeller. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-10b"></a>NIST 800 53 kontrollen CM-10.b

#### <a name="software-usage-restrictions"></a>Programvara användningsbegränsningar

**CM-10.b** organisationen spårar användningen av programvaran och tillhörande dokumentation som skyddas av antal licenser för att styra kopiering och distribution.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Windows- och SQL Server-licenser ingår för resurser som har distribuerats av det här utkastet. Användaren behöver inte separat spåra användning av licenser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-10c"></a>NIST 800 53 kontrollen CM-10.c

#### <a name="software-usage-restrictions"></a>Programvara användningsbegränsningar

**CM-10.c** styr och användning av peer-to-peer-teknik för att säkerställa att den här funktionen inte används för distribution för obehöriga, visa, prestanda eller reproduktion av upphovsrättsskyddat för fildelning-dokument.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns ingen peer-to-peer-fil som delar kapaciteten som distribuerats av det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800 53 kontrollen CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>Programvara användningsbegränsningar | Programvara med öppen källkod

**CM-10 (1)** organisationen upprättar följande begränsningar för användning av programvara med öppen källkod: [tilldelning: organisation användardefinierade begränsningar].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Princip för hantering av kundens på företagsnivå konfiguration kan adressera begränsningar för användning av programvara med öppen källkod. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-11a"></a>NIST 800 53 kontrollen CM-11.a

#### <a name="user-installed-software"></a>Användaren har installerat programvaran

**CM-11.a** organisationen upprättar [tilldelning: organisation definierade principer] för installationen av programvara per användare.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att upprätta en princip som gäller för installationen av programvaran på kunden distribuerade resurser av användare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-11b"></a>NIST 800 53 kontrollen CM-11.b

#### <a name="user-installed-software"></a>Användaren har installerat programvaran

**CM-11.b** organisationen tillämpar principer för Programvaruinstallation via [tilldelning: organisation användardefinierade metoder].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att genomdriva principer för Programvaruinstallation. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-cm-11c"></a>NIST 800 53 kontrollen CM-11.c

#### <a name="user-installed-software"></a>Användaren har installerat programvaran

**CM-11.c** organisationen övervakar principefterlevnad på [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att övervaka efterlevnaden av kunden distribuerade resurser med principer som identifieras i CM-11.a. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800 53 kontrollen CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Installerad programvara användare | Aviseringar om obehöriga installationer

**CM-11 (1)** varningarna information [tilldelning: personal som definierats av organisationen eller roller] när obehörig installationen av programvaran har identifierats.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att tillhandahålla aviseringar när obehörig installationen av programvaran har upptäckts. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |

