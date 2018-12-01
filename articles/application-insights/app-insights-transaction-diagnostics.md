---
title: Azure Application Insights-Transaktionsdiagnostik | Microsoft Docs
description: Application Insights slutpunkt till slutpunkt-transaktionsdiagnostik
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 093f7d7942219aa86c362662ca4c7248ce98386b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721620"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Enhetlig komponentöverskridande transaktionsdiagnostik

Enhetlig diagnostik uppleva automatiskt koreluje s serversidan telemetri från för alla Application Insights övervakas komponenter i en enda vy. Det spelar ingen roll om du har flera resurser med separat instrumenteringsnycklar. Application Insights identifierar underliggande relationen och gör att du kan enkelt diagnostisera programkomponent, beroenden och undantag som gjorde att en transaktion går långsammare eller ett fel.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenterna är oberoende av varandra distribuerbar delar av programmet distribueras/mikrotjänster. Utvecklare och åtgärder team har kod-nivå eller åtkomst till telemetri som genereras av dessa programkomponenter.

* Komponenter skiljer sig från ”observerade” externa beroenden, till exempel SQL, EventHub etc. som ditt team/organisation inte kanske har åtkomst till (kod eller telemetri).
* Komponenter som körs på valfritt antal instanser av server-rollen-behållare.
* Komponenterna kan vara olika Application Insights-instrumenteringsnycklar (även om prenumerationer skiljer sig) eller olika roller som rapporterar till en enda Application Insights-instrumenteringsnyckeln. Den nya miljön visas information för alla komponenter, oavsett hur de har ställts in.

> [!NOTE]
> * **Saknas relaterat objektlänkar?** Alla relaterad telemetri finns i den [upp](#cross-component-transaction-chart) och [nedre](#all-telemetry-with-this-Operation-Id) avsnitt i den vänstra sidan. 

## <a name="transaction-diagnostics-experience"></a>Upplev transaktionsdiagnostik
Den här vyn har fyra viktiga delar: resulterar lista, ett komponentöverskridande transaktion diagram, en tidssekvensen lista över all telemetri som är relaterade till den här åtgärden och informationsfönstret för alla valda telemetriobjekt till vänster.

![Viktiga delar](media/app-insights-transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Komponentöverskridande transaktion diagram

Det här diagrammet innehåller en tidslinje med vågräta stapeldiagram för varaktigheten för begäranden och beroenden för komponenter. Eventuella undantag som samlas in markeras också på tidslinjen.

* Den översta raden i det här diagrammet representerar startpunkten, den inkommande begäran till den första komponenten som heter i den här transaktionen. Varaktigheten är den totala tid det tar för transaktionen att slutföras.
* Alla anrop till externa beroenden är enkel icke-komprimerbart rader med ikoner som representerar Beroendetypen.
* Anrop till andra komponenter finns komprimerbart rader. Varje rad motsvarar en viss åtgärd som anropas på komponenten.
* Som standard, begäran, beroende eller undantag är att du har valt visas på höger sida.
* Välj rader att se dess [information till höger](#details-of-the-selected-telemetry). 

> [!NOTE]
Anrop till andra komponenter har två rader: en rad som representerar det utgående samtalet (beroenden) från komponenten anroparen och den andra raden motsvarar en inkommande begäran i komponenten som kallas. Ledande ikon och distinkta formatering av staplarna varaktighet hjälp för att skilja dem.

## <a name="all-telemetry-with-this-operation-id"></a>All telemetri med detta åtgärds-Id

Det här avsnittet visar vanlig listvy i en tidssekvens av all telemetri för den här transaktionen. Den visar även anpassade händelser och spårningar som inte visas i diagrammet transaktion. Du kan filtrera listan telemetri som genereras av en viss komponent/anrop. Du kan välja alla telemetriobjekt i listan för att se motsvarande [information till höger](#details-of-the-selected-telemetry).

![Tidssekvens med all telemetri](media/app-insights-transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Information om den valda telemetrin

Det här komprimerbart fönstret visar information om alla valda objekt diagrammet transaktion eller i listan. ”Visa alla” visas alla standard attribut som samlas in. Anpassade attribut visas separat under standarduppsättningen. Klicka på ”...” nedan fönstret stack-spårning för att få ett alternativ för att kopiera spårningen. Öppna profiler-spårningar ”eller” öppna ögonblicksbild för felsökning ”visar koden på diagnostik i motsvarande rutor i detalj.

![Undantagsinformation](media/app-insights-transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Sökresultat

Det här komprimerbart fönstret visar de resultat som uppfyller filterkriterierna. Klicka på alla resultat för att uppdatera respektive informationen i 3 avsnitt som anges ovan. Vi försöker hitta exempel som mest sannolikt ha information som är tillgänglig från alla komponenter, även om sampling är aktiv i någon av dem. Dessa visas som ”föreslagna” exempel.

![Sökresultat](media/app-insights-transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler och snapshot debugger

[Application Insights profiler](app-insights-profiler.md) eller [felsökning av ögonblicksbild](app-insights-snapshot-debugger.md) hjälp med kod på servernivå diagnostik av prestanda och fel. Med den här upplevelsen kan du se profiler-spårningar eller ögonblicksbilder från valfri komponent med en enda klickar du på.

Om du inte kunde hämta Profiler fungerar, kontakta **serviceprofilerhelp@microsoft.com**

Om du inte kunde hämta Snapshot Debugger fungerar, kontakta **snapshothelp@microsoft.com**

![Profiler-integrering](media/app-insights-transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

*En enskild komponent visas i diagrammet och visar de andra endast som externa beroenden utan någon information om vad som hände inom dessa komponenter.*

Möjliga orsaker:

* De andra komponenterna är utrustade med Application Insights?
* Använder de senaste stabil Application Insights SDK?
* Har du nödvändig åtkomst till sina telemetri om de här komponenterna separat Application Insights-resurser?

Om du har åtkomst och komponenterna som är utrustade med senaste Application Insights SDK: erna, berätta för oss via översta högra feedback-kanalen.

*Jag ser dubblettrader för beroenden. Förväntas detta?*

För närvarande visar utgående beroendeanropet separat från den inkommande begäran. Vanligtvis två anrop ser identiska ut med endast värdet för varaktighet att skilja sig på grund av nätverket tur och RETUR. Ledande ikon och distinkta formatering av staplarna varaktighet hjälp för att skilja dem. Är den här presentationen av data förvirrande? Ge oss din feedback!

*Vad händer om klockan snedställer över olika komponentinstanser?*

Tidslinjer justeras för klockavvikelser i diagrammet transaktion. Du kan se exakt tidsstämplar i informationsfönstret eller genom att använda Analytics.

*Varför saknas de flesta av frågorna som relaterade objekt i den nya upplevelsen?*

Det här är avsiktligt. Alla relaterade objekt för alla komponenter finns redan på vänster sida (övre och nedre avsnitt). Den nya upplevelsen har två relaterade objekt som inte tas upp i vänster sida: all telemetri från fem minuter före och efter den här händelsen och tidslinje för användaren.
