---
title: Transaktionsdiagnostik för Azure-programinsikter | Microsoft-dokument
description: Application Insights heltäckande transaktionsdiagnostik
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 39c4c51a1bc84e06efac3674b1ee5b487f9e6729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671145"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Enhetlig transaktionsdiagnostik mellan komponenter

Den enhetliga diagnostikupplevelsen korrelerar automatiskt telemetri på serversidan från alla dina Application Insights-övervakade komponenter till en enda vy. Det spelar ingen roll om du har flera resurser med separata instrumenteringsnycklar. Application Insights identifierar den underliggande relationen och låter dig enkelt diagnostisera programkomponenten, beroendet eller undantaget som orsakade en transaktionsavtrollning eller fel.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenter är oberoende av de delar av ditt distribuerade/mikrotjänstprogram. Utvecklare och driftteam har synlighet på kodnivå eller åtkomst till telemetri som genereras av dessa programkomponenter.

* Komponenter skiljer sig från "observerade" externa beroenden som SQL, EventHub etc. som ditt team/organisation kanske inte har åtkomst till (kod eller telemetri).
* Komponenter körs på valfritt antal instanser av server-/roll/behållar.
* Komponenter kan vara separata Instrumenteringsnycklar för Application Insights (även om prenumerationer är olika) eller olika roller som rapporterar till en enda Application Insights-instrumenteringsnyckel. Den nya upplevelsen visar information om alla komponenter, oavsett hur de har ställts in.

> [!NOTE]
> * **Saknar du de relaterade objektlänkarna?** Alla relaterade telemetri är i de [övre](#cross-component-transaction-chart) och [nedre](#all-telemetry-with-this-operation-id) delarna av den vänstra sidan. 

## <a name="transaction-diagnostics-experience"></a>Erfarenhet av transaktionsdiagnostik
Den här vyn har fyra huvuddelar: resultatlista, ett transaktionsdiagram över flera komponenter, en tidssekvenslista över all telemetri som är relaterad till den här åtgärden och informationsfönstret för alla valda telemetriobjekt till vänster.

![Viktiga delar](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Transaktionsdiagram över flera komponenter

Det här diagrammet ger en tidslinje med vågräta staplar för varaktigheten av begäranden och beroenden mellan komponenter. Alla undantag som samlas in markeras också på tidslinjen.

* Den översta raden i det här diagrammet representerar startpunkten, den inkommande begäran till den första komponenten som anropas i den här transaktionen. Varaktigheten är den totala tid det tar för transaktionen att slutföras.
* Alla anrop till externa beroenden är enkla icke-hopfällbara rader, med ikoner som representerar beroendetypen.
* Anrop till andra komponenter är hopfällbara rader. Varje rad motsvarar en specifik åtgärd som anropas vid komponenten.
* Som standard visas begäran, beroendet eller undantaget som du har valt på höger sida.
* Markera en rad om du vill visa [dess information till höger](#details-of-the-selected-telemetry). 

> [!NOTE]
> Anrop till andra komponenter har två rader: en rad representerar det utgående samtalet (beroende) från anroparkomponenten och den andra raden motsvarar den inkommande begäran vid den anropade komponenten. Den ledande ikonen och distinkt styling av varaktighetsstaplar hjälper till att skilja mellan dem.

## <a name="all-telemetry-with-this-operation-id"></a>All telemetri med det här operations-ID:t

I det här avsnittet visas platt listvy i en tidssekvens för all telemetri som är relaterad till den här transaktionen. Den visar också anpassade händelser och spårningar som inte visas i transaktionsdiagrammet. Du kan filtrera den här listan till telemetri som genereras av en viss komponent/anrop. Du kan välja ett telemetriobjekt i den här listan om du vill visa motsvarande [information till höger](#details-of-the-selected-telemetry).

![Tidssekvens för all telemetri](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Information om den valda telemetrin

I det här hopfällbara fönstret visas information om ett markerat objekt från transaktionsdiagrammet eller i listan. "Visa alla" visar alla standardattribut som samlas in. Alla anpassade attribut visas separat under standarduppsättningen. Klicka på "..." under stapelspårningsfönstret för att få ett alternativ för att kopiera spårningen. "Öppna profilerspårningar" eller "Öppna felsökningsögonblicksbild" visar kodnivådiagnostik i motsvarande detaljrutor.

![Information om undantag](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Sökresultat

I det här hopfällbara fönstret visas de andra resultat som uppfyller filtervillkoren. Klicka på något resultat för att uppdatera respektive information de 3 avsnitten som anges ovan. Vi försöker hitta prover som mest sannolikt har information tillgänglig från alla komponenter även om provtagningen gäller i någon av dem. Dessa visas som "föreslagna" prover.

![Sökresultat](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Felsökare för profiler och ögonblicksbilder

[Application Insights profiler](../../azure-monitor/app/profiler.md) eller [snapshot debugger](snapshot-debugger.md) hjälp med kodnivå diagnostik av prestanda och fel problem. Med den här upplevelsen kan du se profilerade spårningar eller ögonblicksbilder från valfri komponent med ett enda klick.

Om du inte kunde få Profiler att fungera, kontakta **\@serviceprofilerhelp microsoft.com**

Om du inte kunde få snapshot debugger fungerar, kontakta **snapshothelp\@microsoft.com**

![Profiler Integration](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

*Jag ser en enda komponent i diagrammet, och de andra visas bara som externa beroenden utan några detaljer om vad som hände inom dessa komponenter.*

Potentiella orsaker:

* Är de andra komponenterna instrumenterade med Application Insights?
* Använder de den senaste stabila Application Insights SDK?
* Om dessa komponenter är separata Application Insights-resurser, har du nödvändig åtkomst till deras telemetri?

Om du har åtkomst och komponenterna är instrumenterade med de senaste Application Insights SDK:erna meddelar du oss via den övre högra feedbackkanalen.

*Jag ser dubblettrader för beroenden. Är detta väntat?*

För närvarande visar vi det utgående beroendeanropet separat från den inkommande begäran. Vanligtvis ser de två anropen identiska ut med att varaktighetsvärdet skiljer sig åt på grund av nätverksresan. Den ledande ikonen och distinkt styling av varaktighetsstaplar hjälper till att skilja mellan dem. Är denna presentation av data förvirrande? Ge oss din feedback!

*Hur är det med klocksnedställningar mellan olika komponentinstanser?*

Tidslinjer justeras för klocksnedställningar i transaktionsdiagrammet. Du kan se de exakta tidsstämplarna i informationsfönstret eller med hjälp av Analytics.

*Varför saknas de flesta relaterade objektfrågor?*

Det här är avsiktligt. Alla relaterade objekt, över alla komponenter, är redan tillgängliga på vänster sida (övre och nedre avsnitt). Den nya upplevelsen har två relaterade objekt som den vänstra sidan inte täcker: all telemetri från fem minuter före och efter den här händelsen och användarens tidslinje.
