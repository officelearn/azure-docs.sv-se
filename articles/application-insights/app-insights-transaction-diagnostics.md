---
title: Azure Application Insights transaktion Diagnostics | Microsoft Docs
description: Programdiagnostik insikter slutpunkt till slutpunkt-transaktion
services: application-insights
documentationcenter: .net
author: SoubhagyaDash
manager: victormu
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: sdash
ms.openlocfilehash: 8c1d8600b7f4aaa1e95f4acfbbdd55fdbfebb8fb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Enhetlig mellan olika komponenter transaktion diagnostik

*Det här upplevelsen är för närvarande under förhandsgranskning och ersätter befintliga diagnostik bladen för serversidan begäranden, beroenden och undantag.*

Förhandsgranskningen introducerar en ny enhetlig diagnostik-miljö som korrelerar serversidan telemetri från automatiskt för alla Application Insights övervakas komponenter i en enda vy. Det spelar ingen roll om du har flera resurser med separat instrumentation nycklar; Application Insights identifierar underliggande relationen och gör att du kan enkelt diagnostisera programkomponent, beroenden eller undantag som orsakade en transaktion långsammare eller ett fel.

## <a name="what-does-component-mean-in-the-context-of-application-insights"></a>Vad innebär komponent i samband med Application Insights?

Komponenter är oberoende av varandra distribuerbara delar i tillämpningsprogrammet distribueras/mikrotjänster. Utvecklare och åtgärder team har kod-nivå eller tillgång till telemetri som genereras av dessa programkomponenter.

* Komponenter som skiljer sig från ”observerade” externa beroenden, till exempel SQL, EventHub etc. som team/organisationen inte kanske har åtkomst till (kod eller telemetri).
* Komponenterna köras i valfritt antal instanser för server-roll-behållare.
* Komponenter kan vara separat Application Insights instrumentation nycklar (även om prenumerationer är olika) eller olika roller som rapporterar till en enda Application Insights instrumentation nyckel. Den nya upplevelsen visar information för alla komponenter, oavsett hur de har ställts in.

> [!Tip]
> Kontrollera att alla komponenter är försett med de senaste stabil Application Insights SDK för bästa resultat. Om det finns olika Application Insights-resurser, se till att du har behörighet att visa sina telemetri.

## <a name="enable-and-access"></a>Aktivera och åtkomst
Aktivera ”Unified information: E2E transaktion diagnostik” från den [förhandsgranskningar lista](app-insights-previews.md)

![Aktivera förhandsgranskning](media/app-insights-e2eTxn-diagnostics/previews.png)

Den här förhandsgranskningen är tillgänglig för serversidan begäranden, beroenden och undantag. Du kan komma åt den nya upplevelsen från **sökresultat**, **prestanda**, eller **fel** prioritering upplevelser. Förhandsgranskningen ersätter motsvarande klassiska information blad.

![Prestandaprover](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>Transaktionen diagnostik upplevelse
Den här vyn har tre viktiga delar: en transaktion mellan komponent diagram, en tidssekvens lista över all telemetri från en viss komponent åtgärd och informationsfönstret för alla valda telemetri objekt till vänster.

![Viktiga delar](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

### <a name="1-cross-component-transaction-chart"></a>[1] mellan olika komponenter transaktion diagram

Det här diagrammet ger en tidslinje med vågräta stapeldiagram för varaktighet begäranden och beroenden för komponenter. Alla undantag som samlas in markeras också på tidslinjen.

* Den översta raden i det här diagrammet representerar startpunkten inkommande begäran till den första komponenten som anropas i den här transaktionen. Varaktigheten är den totala tid det tar att slutföra transaktionen.
* Ett anrop till externa beroenden är enkla ej Komprimerbar rader med ikoner som representerar Beroendetypen.
* Anrop till andra komponenter kan döljas rader. Varje rad motsvarar en specifik åtgärd som anropas på komponenten.
* Som standard visas begäran, beroenden eller undantag markerade i diagrammet.
* Välj en rad om du vill visa information till höger. Klicka på ”Öppna profiler-spårningar” eller ”öppna debug en ögonblicksbild” för koden nivån diagnostik i motsvarande rutor i detalj.

> [!NOTE]
Anrop till andra komponenter har två rader: en rad representerar utgående anropet (beroende) från komponenten anroparen och den andra raden motsvarar en inkommande begäran på komponenten som kallas. Inledande ikon och distinkta stil på fälten varaktighet kan skilja dem.

### <a name="2-time-sequenced-telemetry-of-the-selected-component-operation"></a>[2] tid-sekvenserade telemetri för den valda komponenten igen

En rad som väljs i diagrammet mellan olika komponenter transaktion är kopplad till en åtgärd som anropas på en viss komponent. Den här åtgärden för markerad komponent visas i rubriken för den nedre delen. Öppna det här avsnittet om du vill se en platt tidssekvens av all telemetri som hör till denna åtgärd. Du kan välja ett telemetri objekt i listan om du vill se motsvarande information till höger.

![Tidssekvens av all telemetri](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

### <a name="3-details-pane"></a>[3] informationsfönstret

Det här fönstret visar information för de valda objekten från någon av två delar till vänster. ”Visa alla” visas alla standardattribut som samlas in. Anpassade attribut visas separat under standarduppsättningen.

![Undantagsinformation](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler och snapshot-felsökare

[Application Insights profiler](app-insights-profiler.md) eller [ögonblicksbild felsökare](app-insights-snapshot-debugger.md) hjälp med diagnostiken i koden nivå av prestanda och fel. Du kan se profiler-spårningar eller ögonblicksbilder från alla komponenter med en enda på med den här miljön.

Om du inte kunde hämta Profiler fungerar, kontakta**serviceprofilerhelp@microsoft.com**

Om du inte kunde hämta ögonblicksbild felsökare fungerar, kontakta**snapshothelp@microsoft.com**

![Felsökare integrering](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

*En enda komponent visas i diagrammet och andra som bara visas som externa beroenden utan någon information av vad som hände i dessa komponenter.*

Möjliga orsaker:

* De andra komponenterna är försett med Application Insights?
* Använder de senaste stabil Application Insights SDK?
* Har du nödvändig åtkomst till sina telemetri om de här komponenterna separat Application Insights-resurser?

Om du har åtkomst och komponenterna som är försett med den senaste Application Insights SDK, meddela oss via övre högra feedback-kanal.

*Jag bara har externa beroenden. Bör jag är intresserad av den här förhandsgranskningen?*

Ja. Den nya upplevelsen förenar all relaterad telemetri för serversidan i en enda vy. Äldre detalj bladen ersätts av den här upplevelsen i framtiden, så prova och ge oss dina synpunkter. Här är hur det ser ut för en enskild komponent i transaktion:

![Enskild komponent upplevelse](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*Det finns dubblettrader för beroenden. Förväntas detta?*

För tillfället vi visar utgående beroendeanropet separat från en inkommande begäran. Vanligtvis två anrop ser identiska ut med endast värdet för varaktighet som rundtur olika på grund av nätverket. Inledande ikon och distinkta stil på fälten varaktighet kan skilja dem. Är den här presentationen av data förvirrande? Ge oss dina synpunkter!

*Vad händer om klockan skeva mellan instanser av olika komponenter?*

Tidslinjer justeras för att jämföra transaktionen diagram. Du kan se exakt tidsstämplar i informationsfönstret eller genom att använda Analytics.

*Varför saknas de flesta frågor relaterade objekt i den nya miljön?*

Det här är avsiktligt. Alla relaterade objekt för alla komponenter finns redan på vänster sida (övre och nedre avsnitt). Den nya upplevelsen har två relaterade objekt som inte täcker till vänster: all telemetri från 5 minuter före och efter den här händelsen och en tidslinje för användaren.

*Varför den nya upplevelsen har inte den funktion som jag älskar dig i äldre blad?*

Ge oss feedback! Vi vill fokusera på dina problem innan det här upplevelsen är allmänt tillgänglig då att bli inaktuell äldre vyer. Nu kan du inaktivera Förhandsgranska för att gå tillbaka till äldre blad.

## <a name="known-issues"></a>Kända problem

* Fel-exempel från programavbildningen länka till äldre detalj-blad.
* De autocluster-baserade insikter i sökresultaten länka till äldre detalj-blad.
* Arbetsuppgiftsintegrering är inte tillgänglig i den nya upplevelsen.
