---
title: Säkerhet och efterlevnad modell - FedRAMP Web Applications Automation - systemet och Azure informationens integritet
description: FedRAMP Web Applications Automation - systemet och informationens integritet
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 0eca3c82aea287f6582bd56574512dce5e8e86c7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207136"
---
# <a name="system-and-information-integrity-si"></a>System- och informationens integritet (SI)

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Läs NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

## <a name="nist-800-53-control-si-1"></a>NIST 800 53 kontrollen SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>System och integritet informationsprincip och procedurer

**SI-1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] en system- och integritet-princip som åtgärdar syfte, scope, roller, ansvar, management åtagande samordning av organisationens entiteter och efterlevnad; och procedurer för att underlätta genomförandet av systemet och integritet informationsprincip och associerade system och information integritet kontroller. och granskar och uppdaterar den aktuella system- och integritet principen [tilldelning: organisation definierats frekvens]; och procedurer för system- och integritet [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå system- och integritet principer och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-2a"></a>NIST 800 53 kontrollen SI-2.a

#### <a name="flaw-remediation"></a>Fel-reparation

**SI 2.a** organisationen identifierar, rapporter och korrigerar information system-fel.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Automation- och kontrollservern lösningen för att spåra statusen för uppdateringar för Windows-datorer som distribueras i den här arkitekturen. Från instrumentpanelen visar panelen uppdateringshantering fel reparationsstatus för alla distribuerade Windows-servrar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-2b"></a>NIST 800 53 kontrollen SI-2.b

#### <a name="flaw-remediation"></a>Fel-reparation

**SI 2.b** organisationen testar uppdateringar för programvara och inbyggd programvara som rör fel justering av effektivitet och potentiella sidoeffekter före installationen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att testa uppdateringar som rör fel reparation effektivitet och potentiella sidoeffekter före installation på kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-2c"></a>NIST 800 53 kontrollen SI-2.c

#### <a name="flaw-remediation"></a>Fel-reparation

**SI 2.c** organisationen installerar säkerhetsmässigt uppdateringar av programvara och inbyggd programvara inom [tilldelning: definierad tidsperiod organisation] av versionen av uppdateringarna.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Windows-datorer som distribueras med den här utkast konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen också distribuerar Automation- och kontrollservern lösningen genom vilka distributioner kan skapas för att distribuera korrigeringsfiler till Windows-servrar när det behövs. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-2d"></a>NIST 800 53 kontrollen SI-2.d

#### <a name="flaw-remediation"></a>Fel-reparation

**SI 2.d** organisationen införlivar reparationen för fel i organisationens management konfigurationsprocessen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att inkludera reparationen för fel i konfigurationshantering. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-2-1"></a>NIST 800 53 kontrollen SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>Fel reparation | Central hantering

**SI-2 (1)** organisationen hanterar centralt fel reparation processen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Automation- och kontrollservern lösningen för att spåra statusen för uppdateringar för Windows-datorer som distribueras i den här arkitekturen. Från instrumentpanelen visar panelen uppdateringshantering fel reparationsstatus för alla distribuerade Windows-servrar. Distributioner kan skapas för att distribuera korrigeringsfiler till Windows-servrar när det behövs. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-2-2"></a>NIST 800 53 kontrollen SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Fel reparation | Reparationsstatus för automatisk fel

**SI-2 (2)** organisationen använder automatisk mekanismer [tilldelning: organisation definierats frekvens] att avgöra tillståndet för information systemkomponenter avseende reparationen för fel.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Automation- och kontrollservern lösningen för att spåra statusen för uppdateringar för Windows-datorer som distribueras i den här arkitekturen. För varje hanterad Windows-dator utförs en genomsökning två gånger per dag. Var 15:e minut anropas Windows API för att fråga efter den senaste uppdateringstiden för att fastställa om statusen har ändrats, och i så fall om en fullständig genomsökning har initierats. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-2-3a"></a>NIST 800 53 styra SI-2 (3) en

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Fel reparation | Tid för att reparera fel / riktmärken för korrigerande åtgärder

**SI-2 (3) en** organisationen mäter tiden mellan fel identifiering och reparationen för fel.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för hälsostatus fel inom kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-2-3b"></a>NIST 800 53 styra SI-2 (3) .b

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Fel reparation | Tid för att reparera fel / riktmärken för korrigerande åtgärder

**SI-2 (3) .b** organisationen upprättar [tilldelning: organisation definierats prestandamått] för att vidta lämpliga åtgärder.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på företagsnivå prestandamått för fel reparation processer. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-3a"></a>NIST 800 53 kontrollen SI-3.a

#### <a name="malicious-code-protection"></a>Skadlig kod Protection

**SI 3.a** organisationen använder skyddet för skadlig kod vid information system ingång och utgång punkter att identifiera och utrota skadlig kod.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar värdbaserade program mot skadlig kod skydd för alla distribuerade virtuella Windows-datorer implementeras med hjälp av Microsoft Antimalware-tillägget för virtuell dator. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-3b"></a>NIST 800 53 kontrollen SI-3.b

#### <a name="malicious-code-protection"></a>Skadlig kod Protection

**SI 3.b** organisationen uppdateras skadlig kod skyddet när nya versioner är tillgängliga i enlighet med organisationens configuration management principer och procedurer.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar värdbaserade program mot skadlig kod skydd för alla distribuerade virtuella Windows-datorer implementeras med hjälp av Microsoft Antimalware-tillägget för virtuell dator. Det här tillägget är konfigurerad att automatiskt uppdatera båda antimalware-motorn och skydd signaturer som blir tillgängliga versionen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-3c"></a>NIST 800 53 kontrollen SI-3.c

#### <a name="malicious-code-protection"></a>Skadlig kod Protection

**SI 3.c** organisationen konfigurerar skadlig kod protection mekanismer för att utföra regelbundna sökningar av informationssystem [tilldelning: organisation definierats frekvens] och realtid genomsökningar av filer från externa källor vid [markeringen (en eller Mer). slutpunkten; nätverk in-/ utgång points] när filerna har hämtats, öppnas eller köras i enlighet med organisationens säkerhetsprincip; och [markeringen (en eller flera): blockera skadlig kod; karantän skadlig kod; skicka aviseringen till administratör. [Tilldelning: organisation definierats åtgärd]] som svar på identifiering av skadlig kod.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar värdbaserade program mot skadlig kod skydd för alla distribuerade virtuella Windows-datorer implementeras med hjälp av Microsoft Antimalware-tillägget för virtuell dator. Det här tillägget är konfigurerad att både i realtid och regelbundna sökningar (varje vecka), automatiskt uppdatera båda antimalware-motorn och skydd signaturer och utföra automatiska åtgärder. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-3d"></a>NIST 800 53 kontrollen SI-3.d

#### <a name="malicious-code-protection"></a>Skadlig kod Protection

**SI 3.d** organisationen adresser mottagandet av falska positiva identifieringar under identifiering av skadlig kod och utrotning och resulterande inverkan på tillgänglighet av den.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att skydda mot skadlig kod. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-3-1"></a>NIST 800 53 kontrollen SI-3 (1)

#### <a name="malicious-code-protection--central-management"></a>Skadlig kod Protection | Central hantering

**SI – 3 (1)** organisationen hanterar centralt skyddet för skadlig kod.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar värdbaserade program mot skadlig kod skydd för alla distribuerade virtuella Windows-datorer implementeras med hjälp av Microsoft Antimalware-tillägget för virtuell dator. Azure tillhandahåller en centraliserad möjligheten att granska den aktuella statusen för program mot skadlig kod. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-3-2"></a>NIST 800 53 kontrollen SI – 3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>Skadlig kod Protection | Automatiska uppdateringar

**SI-3.2** systemets information uppdateras automatiskt skyddet för skadlig kod.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar värdbaserade program mot skadlig kod skydd för alla distribuerade virtuella Windows-datorer implementeras med hjälp av Microsoft Antimalware-tillägget för virtuell dator. Det här tillägget är konfigurerad att automatiskt uppdatera båda antimalware-motorn och skydd signaturer som blir tillgängliga versionen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-3-7"></a>NIST 800 53 kontrollen SI – 3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Skadlig kod Protection | Nonsignature-baserad identifiering

**SI-3.7** informationssystem implementerar nonsignature-baserade skadlig kod identifieringsmetoder.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar värdbaserade program mot skadlig kod skydd för alla distribuerade virtuella Windows-datorer implementeras med hjälp av Microsoft Antimalware-tillägget för virtuell dator. Det här tillägget har konfigurerats för att utföra heuristisk identifiering. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-4a"></a>NIST 800 53 kontrollen SI-4.a

#### <a name="information-system-monitoring"></a>Information systemövervakning

**SI 4.a** organisationen övervakar det system för att identifiera attacker och indikatorer för potentiella attacker i enlighet med [tilldelning: organisation definierade övervakning mål]; och obehörig lokal, nätverks- och fjärråtkomst anslutningar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Log Analytics och säkerhet och granska lösningen. Den här lösningen ger en omfattande vy av säkerhetstillståndet attacker och indikatorer för potentiella attacker. Säkerhet och granska instrumentpanelen ger övergripande inblick i säkerhetsläget för distribuerade resurser med tillgängliga data över av distribuerade hanteringslösningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-4b"></a>NIST 800 53 kontrollen SI-4.b

#### <a name="information-system-monitoring"></a>Information systemövervakning

**SI 4.b** organisationen identifierar obehörig användning av den via [tilldelning: organisation användardefinierade tekniker och metoder].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar lösningen säkerhets- och granskning. Identifiera och åtkomst till domänen innehåller en instrumentpanel med en översikt över information identitet systemtillstånd, inklusive antalet misslyckade försök att logga in och antal konton som är inloggade i. Informationen i den här instrumentpanelen kan hjälpa identifiering av potentiellt misstänkt aktivitet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-4c"></a>NIST 800 53 kontrollen SI-4.c

#### <a name="information-system-monitoring"></a>Information systemövervakning

**SI 4.c** organisationen distribuerar övervakning enheter strategiskt inom systemet för att samla in organisation fastställa viktig information, och på ad hoc-platser i systemet att spåra vissa typer av transaktioner intresse för organisationen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Log Analytics och säkerhet och granska lösningen. Säkerhet och granska instrumentpanelen ger övergripande inblick i säkerhetsläget för distribuerade resurser med tillgängliga data över av distribuerade hanteringslösningar, inklusive inblick i VM-operativsystemet övervakningsdata. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-4d"></a>NIST 800 53 kontrollen SI-4.d

#### <a name="information-system-monitoring"></a>Information systemövervakning

**SI 4.d** organisationen skyddar informationen som hämtas från övervakning av intrångsidentifiering verktyg från obehörig åtkomst, ändras eller tas bort.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Logiska åtkomstkontroller för att skydda informationen av övervakningen inom det här utkastet från obehörig åtkomst, ändras eller tas bort. Azure Active Directory tillämpar godkända logiska åtkomst med hjälp av rollbaserad gruppmedlemskap. Möjlighet att se övervakningsinformation och använda övervakningsverktyg kan vara begränsad till användare som kräver dessa behörigheter. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-4e"></a>NIST 800 53 kontrollen SI-4.e

#### <a name="information-system-monitoring"></a>Information systemövervakning

**SI 4.e** Organisationen ökar nivå av övervakning av aktivitet när det är en indikation på ökad risk för organisationens åtgärder och tillgångar, personer, andra organisationer eller av landet enligt lag tvingande information affärsinformation eller andra trovärdig informationskällor.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att övervaka kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-4f"></a>NIST 800 53 kontrollen SI-4.f

#### <a name="information-system-monitoring"></a>Information systemövervakning

**SI 4.f** organisationen hämtar juridiska åsikt avseende informationssystem övervakningen enligt gällande federal lagar, Executive order, direktiven, principer och förordningar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att övervaka kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-4g"></a>NIST 800 53 kontrollen SI-4.g

#### <a name="information-system-monitoring"></a>Information systemövervakning

**SI 4.g** organisationen ger [tilldelning: organisation definierats informationssystem övervakningsinformation] till [tilldelning: personal som definierats av organisationen eller roller] [markeringen (en eller flera): efter behov. [Tilldelning: organisation definierats frekvens]].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att övervaka kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-1"></a>NIST 800 53 kontrollen SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Information systemövervakning | Systemomfattande intrång identifiering System

**SI-4.1** organisationen ansluter och konfigurerar enskilda intrång identifieringsverktyg i en informationssystem systemomfattande intrång identifiering.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att övervaka kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-2"></a>NIST 800 53 kontrollen SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Information systemövervakning | Automatiserade verktyg för analys i realtid

**SI-4.2** organisationen använder automatiserade verktyg för att stödja nära realtid analys av händelser.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Log Analytics och olika hanteringslösningar, inklusive säkerhet och granska lösningen. Log Analytics ger nästan realtid analys av händelser över distribuerade resurser. Management-lösningar ger en omfattande vy av säkerhetstillståndet lösning domäner. Log Analytics ger inblick i säkerhetsläget för distribuerade resurser med tillgängliga data över av distribuerade hanteringslösningar. Logganalys kan konfigureras för att generera varningar baserat på definierade villkor. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800 53 kontrollen SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Information systemövervakning | Kommunikation för inkommande och utgående trafik

**SI-4.4** systemet som övervakar inkommande och utgående kommunikation trafik [tilldelning: organisation definierats frekvens] för ovanliga eller obehörig aktiviteter eller villkor.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att övervaka kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-5"></a>NIST 800 53 kontrollen SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>Information systemövervakning | Systemgenererade aviseringar

**SI-4.5** varningarna information [tilldelning: personal som definierats av organisationen eller roller] när följande indikationer på hot eller potentiella hot inträffar: [tilldelning: organisation definierats röjande indikatorer].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar olika hanteringslösningar, inklusive säkerhet och granska lösningen. Log Analytics ger nästan realtid analys av händelser över distribuerade resurser. Management-lösningar ger en omfattande vy av säkerhetstillståndet lösning domäner. Logganalys kan konfigureras för att generera varningar baserat på definierade villkor. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-11"></a>NIST 800 53 kontrollen SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Information systemövervakning | Analysera kommunikation trafik avvikelser

**SI-4 (11)** organisationen analyserar utgående kommunikation trafik på den externa gränsen för systemet och valt [tilldelning: organisation definierats inre pekar i systemet (till exempel undernät, delsystem)] till identifiera avvikelser.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att analysera kommunikation trafik avvikelser för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-14"></a>NIST 800 53 kontrollen SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Information systemövervakning | Trådlösa intrångsidentifiering

**SI-4 (14)** organisation sådana försöker en trådlösa intrång identifiering systemet för att identifiera falska trådlösa enheter och för att identifiera attacker och potentiella kompromisser/säkerhetsintrång informationssystem.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Ingen kund-kontrollerade maskinvara, inklusive trådlösa enheter tillåts i Azure-datacenter. |
| **Providern (Microsoft Azure)** | Microsoft Azure övervakar regelbundet för falsk trådlösa signaler kvartalsvis enligt beskrivningen i AC-18. <br /> Microsoft Azure implementerar den här kontrollen för IaaS- och PaaS-kunder. |


 ### <a name="nist-800-53-control-si-4-16"></a>NIST 800 53 kontrollen SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Information systemövervakning | Korrelera övervakningsinformation

**SI-4 (16)** organisationen korrelerar information från övervakningsverktyg anställda i hela informationssystemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar Log Analytics och olika hanteringslösningar, inklusive säkerhet och granska lösningen. Log Analytics ger inblick i säkerhetsläget för distribuerade resurser med tillgängliga data över av distribuerade hanteringslösningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-18"></a>NIST 800 53 kontrollen SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Information systemövervakning | Analysera trafik / konvertera Exfiltration

**SI-4 (18)** organisationen analyserar utgående kommunikation trafik i kolumnrubrikens externa informationssystem (d.v.s. system perimeternätverk) och vid [tilldelning: organisation definierats inre pekar inom system (t.ex. undersystem, undernät)] att identifiera konvertera exfiltration av information.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att analysera kommunikationstrafiken för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-19"></a>NIST 800 53 kontrollen SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Information systemövervakning | Personer som utgör större Risk

**SI-4 (19)** organisation implementerar [tilldelning: organisation definierats ytterligare övervakning] av personer som har identifierats av [tilldelning: organisation definierats källor] som en ökad nivå av risk.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för att övervaka personer som innebär en större risk. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-20"></a>NIST 800 53 kontrollen SI-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>Information systemövervakning | Privilegierade användare

**SI-4 (20)** organisation implementerar [tilldelning: organisation definierats ytterligare övervakning] Privilegierade användare.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för att övervaka Privilegierade användare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-22"></a>NIST 800 53 kontrollen SI-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Information systemövervakning | Obehörig nätverkstjänster

**SI-4 (22)** information upptäcks nätverkstjänster som inte har behörighet eller godkänts av [tilldelning: organisation definierats auktorisering eller godkännande processer] och [markeringen (en eller flera): granskningar, aviseringar [tilldelning: personal som definierats av organisationen eller roller]].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att upptäcka otillåten nätverkstjänster. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-23"></a>NIST 800 53 kontrollen SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>Information systemövervakning | Värd-baserade enheter

**SI-4 (23)** organisation implementerar [tilldelning: organisation definierats värdbaserad kontrollmekanismer] vid [tilldelning: organisation definierats information systemkomponenter].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet samlar in övervakningsdata från distribuerade resurser, inklusive data från värdbaserade övervakningsfunktionerna. Microsoft Monitoring Agent är installerad på alla Windows-datorer för att samla in övervakningsdata som används av logganalys och andra hanteringslösningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-4-24"></a>NIST 800 53 kontrollen SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Information systemövervakning | Indikatorer för Kompromettering

**SI-4 (24)** informationssystemet identifierar, samlar in, distribuerar och använder indikatorer för angrepp.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för identifiering, samla in, distribuera och använda indikatorer för kompromettering till kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-5a"></a>NIST 800 53 kontrollen SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>Säkerhetsaviseringar och rekommendationerna direktiven

**SI 5.a** organisationen som tar emot information system säkerhetsaviseringar och rekommendationerna direktiven från [tilldelning: organisation definierats externa organisationer] kontinuerligt.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera säkerhetsaviseringar, rekommendationerna och riktlinjer för kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-5b"></a>NIST 800 53 kontrollen SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>Säkerhetsaviseringar och rekommendationerna direktiven

**SI 5.b** organisationen genererar interna säkerhetsaviseringar och rekommendationerna direktiv som anses vara nödvändigt.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera säkerhetsaviseringar, rekommendationerna och riktlinjer för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-5c"></a>NIST 800 53 kontrollen SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>Säkerhetsaviseringar och rekommendationerna direktiven

**SI 5.c** organisationen sprider säkerhetsaviseringar och rekommendationerna direktiv: [markeringen (en eller flera): [tilldelning: personal som definierats av organisationen eller roller]; [Tilldelning: organisation definierats element i organisationen]; [Tilldelning: organisation definierats externa organisationer]].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera säkerhetsaviseringar, rekommendationerna och riktlinjer för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-5d"></a>NIST 800 53 kontrollen SI-5.d

#### <a name="security-alerts-advisories-and-directives"></a>Säkerhetsaviseringar och rekommendationerna direktiven

**SI 5.d** organisationen implementerar säkerhet direktiven i enlighet med etablerade tidsramar eller meddelar den utfärdande organisationen graden av inkompatibilitet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera säkerhetsaviseringar, rekommendationerna och riktlinjer för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-5-1"></a>NIST 800 53 kontrollen SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Säkerhetsaviseringar och rekommendationerna direktiven | Automatiserade aviseringar och rekommendationerna

**SI-5 (1)** organisationen använder automatisk mekanismer för att se säkerhet aviseringen och advisory information som är tillgängliga i hela organisationen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera säkerhetsaviseringar, rekommendationerna och riktlinjer för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-6a"></a>NIST 800 53 kontrollen SI-6.a

#### <a name="security-function-verification"></a>Funktionen säkerhetskontroll

**SI 6.a** informationssystem verifierar att av [tilldelning: organisation definierats säkerhetsfunktioner].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för funktionen säkerhetsverifiering för kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-6b"></a>NIST 800 53 kontroll-SI-6 b

#### <a name="security-function-verification"></a>Funktionen säkerhetskontroll

**6 SI b** information utförs den här kontrollen [markeringen (en eller flera): [tilldelning: organisation definierats övergående systemtillstånd]; vid kommandot av användare med rätt behörighet; [Tilldelning: organisation definierats frekvens]].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för funktionen säkerhetsverifiering för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-6c"></a>NIST 800 53 kontrollen SI-6

#### <a name="security-function-verification"></a>Funktionen säkerhetskontroll

**SI 6** meddelar informationssystemet [tilldelning: personal som definierats av organisationen eller roller] av misslyckade säkerhet verifieringstester.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för funktionen säkerhetsverifiering för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-6d"></a>NIST 800 53 kontrollen SI-6.d

#### <a name="security-function-verification"></a>Funktionen säkerhetskontroll

**SI 6.d** systemets information [markeringen (en eller flera): stängs systemet; startar om informationssystemet. [Tilldelning: organisation användardefinierade alternativa aktivitet(er)]] När avvikelser upptäcks.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för funktionen säkerhetsverifiering för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-7"></a>NIST 800 53 kontrollen SI-7

#### <a name="software-firmware-and-information-integrity"></a>Programvara, inbyggd programvara och informationens integritet

**SI 7** organisationen använder integritet verifiering verktyg för att identifiera obehöriga ändringar av [tilldelning: organisation definierats information om programvara, inbyggd programvara och].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här utkast kör Windows-operativsystem. Windows tillhandahåller realtid filen integritet verifiering, skydd och återställning av viktiga systemfiler som installeras som en del av Windows eller auktoriserade systemuppdateringar via Windows Resource Protection WRP ()-funktionen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-7-1"></a>NIST 800 53 kontrollen SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Programvara, inbyggd programvara och informationens integritet | Integritetskontroller

**SI-7.1** information utförs en integritetskontroll av [tilldelning: organisation definierats information om programvara, inbyggd programvara och] [markeringen (en eller flera): vid start; på [tilldelning: organisation definierats övergående tillstånd eller säkerhet – relevanta händelser]; [Tilldelning: organisation definierats frekvens]].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här utkast kör Windows-operativsystem. Windows tillhandahåller realtid filen integritet verifiering, skydd och återställning av viktiga systemfiler som installeras som en del av Windows eller auktoriserade systemuppdateringar via Windows Resource Protection WRP ()-funktionen. WRP kan realtid integritetskontroll. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-7-2"></a>NIST 800 53 kontrollen SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Programvara, inbyggd programvara och informationens integritet | Automatiserade aviseringar integritet överträdelser av

**SI-7.2** organisationen använder automatiserade verktyg som ger avisering till [tilldelning: personal som definierats av organisationen eller roller] vid identifiering av avvikelser under verifiering.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här utkast kör Windows-operativsystem. Windows tillhandahåller realtid filen integritet verifiering, skydd och återställning av viktiga systemfiler som installeras som en del av Windows eller auktoriserade systemuppdateringar via Windows Resource Protection WRP ()-funktionen.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-7-5"></a>NIST 800 53 kontrollen SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Programvara, inbyggd programvara och informationens integritet | Automatiska svar på integritet överträdelser

**SI-7.5** systemet automatiskt [markeringen (en eller flera): stängs systemet; startar om informationssystemet; implementerar [tilldelning: organisation definierats säkerhetsåtgärder]] när integritet överträdelser är identifierade.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att automatiskt svara på integritet överträdelser inom kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-7-7"></a>NIST 800 53 kontrollen SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Programvara, inbyggd programvara och informationens integritet | Integrering av identifiering och svar

**SI-7 (7)** organisationen inkluderar identifiering av obehöriga [tilldelning: organisation definierats säkerhetsmässigt ändringar i systemet] till organisationens incidenter kapaciteten.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att skydda integriteten för programvaran och information för kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-7-14"></a>NIST 800 53 kontrollen SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Programvara, inbyggd programvara och informationens integritet | Binär eller maskin körbar kod

**SI-7 (14)** organisationen förhindrar användning av binär eller datorn körbara koden från källor med begränsad eller ingen garanti och utan tillhandahållande av källkoden, och ger undantag i källan kod krav endast för tvingande uppdrag / operativa krav och med godkännande från den utfärdande officiellt.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå system- och integritet procedurer kan fastställa kraven för att hämta källkoden för binär eller maskin körbar kod från vissa källor. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-8a"></a>NIST 800 53 kontrollen SI-8.a

#### <a name="spam-protection"></a>Skicka meddelanden till skydd

**SI 8.a** skyddet organisation sådana skräppost vid information system ingång och utgång punkter att upptäcka och vidta åtgärder för oönskade meddelanden.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns ingen e-postservrar distribueras som en del av det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-8b"></a>NIST 800 53 kontrollen SI-8.b

#### <a name="spam-protection"></a>Skicka meddelanden till skydd

**SI 8.b** organisation uppdateringar skräppost skyddsmekanismer när nya versioner är tillgängliga i enlighet med organisationens configuration management principer och procedurer.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns ingen e-postservrar distribueras som en del av det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-8-1"></a>NIST 800 53 kontrollen SI-8 (1)

#### <a name="spam-protection--central-management"></a>Skicka meddelanden till skydd | Central hantering

**SI-8.1** organisationen hanterar centralt skräppost skyddet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns ingen e-postservrar distribueras som en del av det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-si-8-2"></a>NIST 800 53 kontrollen SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>Skicka meddelanden till skydd | Automatiska uppdateringar

**SI-8 (2)** systemets information uppdateras automatiskt skräppost skyddet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns ingen e-postservrar distribueras som en del av det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-10"></a>NIST 800 53 kontrollen SI-10

#### <a name="information-input-validation"></a>Verifiering av indata information

**SI 10** informationssystemet kontrollerar giltigheten för [tilldelning: organisation definierats information indata].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för information verifiering av indata för kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-11a"></a>NIST 800 53 kontrollen SI-11.a

#### <a name="error-handling"></a>Felhantering

**SI 11.a** information genereras felmeddelanden som innehåller information som behövs för korrigeringsåtgärder utan att avslöja information som kan utnyttjas av motståndare.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Resurser som har distribuerats av den här modell av kommersiella operativsystem och program. Den här programvaran använder branschens bästa praxis för att kontrollera känslig information som inte visas i felmeddelanden. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-11b"></a>NIST 800 53 kontrollen SI-11.b

#### <a name="error-handling"></a>Felhantering

**SI 11.b** felmeddelanden visar endast i systemet [tilldelning: personal som definierats av organisationen eller roller].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Resurser som har distribuerats av den här modell av kommersiella operativsystem och program. Den här programvaran använder branschens bästa praxis för felmeddelanden som är lämpliga i samband med användning som tar emot meddelandet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-12"></a>NIST 800 53 kontrollen SI – 12

#### <a name="information-handling-and-retention"></a>Information hantering och lagring.

**SI 12** organisationen hanterar och behåller information inom informationssystem och information utdata från systemet i enlighet med tillämpliga federal lagar Executive order, direktiven, principer, förordningar, standarder, och operativa krav.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera och behåller information i kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara) och information utdata från de här resurserna. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-si-16"></a>NIST 800 53 kontrollen SI-16

#### <a name="memory-protection"></a>Minnesskydd

**SI 16** implementerar systemet [tilldelning: organisation definierats säkerhetsåtgärder] till att skydda dess minne från obehörig kodkörning.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här utkast kör Windows-operativsystem. Windows har skydd för förhindrar körning av kod i begränsade minnesplatser: Nej köra (NX), adress utrymme Layout slumpmässig (ASLR) och dataexekveringsskydd (DEP). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |
