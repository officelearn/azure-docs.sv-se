---
title: Förhandsanvändningsfall - Insikter i Azure Time Series | Microsoft-dokument
description: Läs mer om förhandsversion av Azure Time Series Insights-användningsfall.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 50ac2a728750c6b01dfc57fa7e20df25c856395a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087386"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Användarsituationer i förhandsversionen av Azure Time Series Insights

Den här artikeln sammanfattar flera vanliga användningsfall för förhandsversionen av Azure Time Series Insights. Rekommendationerna i den här artikeln fungerar som en utgångspunkt för att utveckla dina program och lösningar med Time Series Insights.

I den här artikeln besvaras följande frågor:

* Vilka är de vanligaste användningsfallen för Time Series Insights?
* Vilka är fördelarna med att använda Time Series Insights för [datautforskning och visuell avvikelseidentifiering?](#data-exploration-and-visual-anomaly-detection)
* Vilka är fördelarna med att använda Time Series Insights för [operativ analys och processeffektivitet?](#operational-analysis-and-driving-process-efficiency)
* Vilka är fördelarna med att använda Time Series Insights för [avancerad analys?](#advanced-analytics)

En översikt över dessa användningsscenarier beskrivs i följande avsnitt.

## <a name="introduction"></a>Introduktion

Azure Time Series Insights är ett heltäckande plattforms-som-en-tjänst-erbjudande. Den används för att samla in, bearbeta, lagra, analysera och fråga mycket kontextualiserade tidsserieoptimerade IoT-skalningsdata. Time Series Insights passar perfekt för ad hoc-datagranskning och operativ analys. Time Series Insights är ett unikt utökningsbart, anpassat tjänsteerbjudande som uppfyller de breda behoven hos industriella IoT-distributioner.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Identifiering av datautforskning och visuell anomali

Utforska och analysera miljarder händelser direkt för att upptäcka avvikelser och upptäcka dolda trender i dina data. Time Series Insights ger prestanda i nära realtid för dina IoT- och DevOps-analysarbetsbelastningar.

[![Data explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

De flesta kunder är överens om att den minimala tid som krävs för att få insikt är en av de enastående funktionerna i Time Series Insights:

* Time Series Insights kräver ingen initial dataförberedelse. 
* Det fungerar snabbt för att ansluta dig till miljarder händelser i dina Azure IoT Hub- eller Azure Event Hubs-instanser på några minuter. 
* När du är ansluten kan du visualisera och analysera miljarder händelser för att upptäcka avvikelser och upptäcka dolda trender i dina data.

Time Series Insights är intuitivt och enkelt att använda. Du kan interagera med dina data utan att skriva en enda kodrad. Det finns inte heller något nytt språk som du behöver lära dig, även om Time Series Insights tillhandahåller ett detaljeradt textbaserat frågespråk för avancerade användare som är bekanta med SQL. Det ger också välja-och-klicka utforskning för nybörjare.

Kunderna kan dra nytta av hastigheten för att diagnostisera tillgångsrelaterade problem snabbt. De kan utföra DevOps-analys för att komma till grundorsaken till en bugg i en IoT-lösning. De kan också identifiera områden att flagga för vidare undersökning som en del av sina datavetenskap initiativ. 

Det finns tre huvudsakliga sätt att interagera med data som lagras i Time Series Insights:

* Det första och enklaste sättet att komma igång är med Time Series Insights Preview Explorer. Du kan använda den för att snabbt visualisera alla dina IoT-data på ett ställe. Det ger verktyg som värmekartan som hjälper dig att upptäcka avvikelser i dina data. Det ger också en perspektivvy. Använd den för att jämföra upp till fyra vyer från en eller flera Time Series Insights-miljöer i en enda instrumentpanel. Instrumentpanelen ger dig en översikt över dina tidsseriedata på alla dina platser. Läs mer om [förhandsgranskningsutforskaren Förhandsgranskning av Time Series Insights](./time-series-insights-update-explorer.md). Om du vill planera din time series insights-miljö läser du [planering av Time Series Insights.](./time-series-insights-update-plan.md)

* Det andra sättet att börja är att använda JavaScript SDK för att snabbt bädda in kraftfulla diagram och diagram i ditt webbprogram. Med bara några rader kod kan du skapa kraftfulla frågor. Använd dem för att fylla i linjediagram, cirkeldiagram, stapeldiagram, värmekartor, datarutnät med mera. Alla dessa element finns utanför boxen med hjälp av SDK. SDK abstraherar också Time Series Insights-fråge-API:er. Du kan använda dem för att skapa SQL-liknande predikat för att fråga de data som du vill visa på en instrumentpanel. För hybridpresentationslagerlösningar erbjuder Time Series Insights parameteriserade webbadresser. De ger sömlösa anslutningspunkter med Time Series Insights Preview Explorer för djupdykningar i data.

  * Läs om [Tidsseriestatistiken JS-klientbiblioteket](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) och [tidsseriestatistikklientdokumentationen](https://github.com/Microsoft/tsiclient) om du vill veta mer om JavaScript SDK.

  * Läs mer om att dela webbadresser och det nya användargränssnittet genom att granska [Visualisera data i utforskaren för förhandsversionen av Azure Time Series Insights.](time-series-insights-update-explorer.md)

* Det tredje sättet att börja är att använda kraftfulla API:er för att fråga data som lagras i Time Series Insights. Time Series Insights har temporala `to` `first`operatorer `last`som `from`, , och . Den har aggregeringar och `average` `min`omvandlingar `split by` `order by`som `DateHistogram`, , `max`, och . Den har också filtreringsoperatorer `or` `greater than`som `has`, `in`, `and`, och `REGEX`. Alla dessa operatörer gör det möjligt för nedströmsprogram att snabbt hitta intressanta trender och mönster i dina data. Använd dem för att fylla i egenodlade visualiseringar för att upptäcka avvikelser.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Operativ analys och drivande processeffektivitet

Använd Time Series Insights för att övervaka hälso-, användnings- och prestanda för utrustning i stor skala. Time Series Insights är ett enkelt sätt att mäta driftseffektivitet. Time Series Insights hjälper till att hantera olika och oförutsägbara IoT-arbetsbelastningar utan att offra inmatning eller frågeprestanda.

[![Översikt](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streaming och kontinuerlig bearbetning av data som kommer från operativa processer kan framgångsrikt omvandla alla företag om det kombineras med rätt teknik eller lösning. Ofta är dessa lösningar en kombination av flera system. De möjliggör utforskning och analys av data som ändras ständigt, särskilt i IoT-sfären, och delar ett gemensamt mönster.

Dessa mönster börjar ofta med IoT-aktiverade plattformar som matar in miljarder händelser från enheter och sensorer som sträcker sig över olika språk. Dessa system bearbetar och analyserar strömmande data för att härleda insikter och åtgärder i realtid. Data arkiveras vanligtvis till varmt och kallt lager för nära realtid och batchanalys.

Data som samlas in går igenom en serie bearbetning för att rengöra och kontextualisera dem för scenarier för efterföljande frågor och analyser. Azure erbjuder omfattande tjänster som kan tillämpas på IoT-scenarier som underhåll av tillgångar och tillverkning. Dessa tjänster omfattar Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning och Power BI.

Lösningsarkitektur kan uppnås på följande sätt:

* Inta data via IoT Hub eller Event Hubs för förstklassig säkerhet, dataflöde och svarstid.
* Utför databehandling och beräkningar. Kanalisera intövd data via tjänster som Stream Analytics, Logic Apps och Azure Functions. Vilken tjänst du använder beror på vilka specifika databehandlingsbehov som finns.
* Beräknade signaler från bearbetningspipelinen överförs till Time Series Insights för lagring och analys.

Time Series Insights erbjuder datautforskning i nära realtid och tillgångsbaserade insikter över historiska data. Beroende på dina affärsbehov kan MapReduce- och Hive-jobb köras på data som lagras i Time Series Insights genom att ansluta Time Series Insights till Azure HDInsight. Data som lagras i Time Series Insights är tillgängliga för Power BI och andra kundprogram via Time Series Insights offentliga api:er för ytfrågefrågor. Dessa data kan användas för djupa affärs- och driftinformationsscenarier.

## <a name="advanced-analytics"></a>Avancerad analys

Integrera med avancerade analystjänster som Machine Learning och Azure Databricks. Time Series Insights inträder rådata från miljontals enheter. Det lägger till kontextuella data som kan förbrukas sömlöst av en uppsättning Azure analytics-tjänster.

[![Analys](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Avancerad analys och maskininlärning förbrukar och bearbetar stora mängder data. Dessa data används för att fatta datadrivna beslut och utföra prediktiv analys. I IoT-användningsfall lär sig avancerade analysalgoritmer från data som samlas in från miljontals enheter. Dessa enheter överför data flera gånger i sekunden. Data som samlas in från IoT-enheter är råa. Den saknar kontextuell information såsom enhetens placering och enheten för sensoravläsningen. Därför är rådata svårt att använda direkt för avancerade analyser.

Time Series Insights överbryggar klyftan mellan IoT-data och avancerad analys på två enkla och kostnadseffektiva sätt:

* För det första samlar Time Series Insights in råtelemetridata från miljontals enheter med hjälp av IoT Hub. Den berikar data med kontextuell information och omvandlar data till ett parkettformat. Det här formatet kan enkelt integreras med andra avancerade analystjänster, till exempel Machine Learning, Azure Databricks och program från tredje part.

    Time Series Insights kan fungera som sanningens källa för alla data i en organisation. Det skapar en central databas för nedströmsanalysarbetsbelastningar att använda. Eftersom Time Series Insights är en lagringstjänst i nära realtid kan avancerade analysmodeller lära sig kontinuerligt från inkommande IoT-telemetridata. Som ett resultat kan modellerna göra mer exakta förutsägelser.

* För det andra kan utdata från maskininlärnings- och förutsägelsemodeller matas in i Time Series Insights för att visualisera och lagra deras resultat. Den här proceduren hjälper organisationer att optimera och justera sina modeller. Time Series Insights gör det enkelt att visualisera strömmande telemetridata på samma plan som de tränade modellutgångarna. På så sätt hjälper det datascience-team att upptäcka avvikelser och identifiera mönster.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [förhandsgranskningsutforskaren Förhandsgranskning av Time Series Insights](./time-series-insights-update-explorer.md).
* Läs [Time Series Insights Förhandsgranska planering](./time-series-insights-update-plan.md) för att planera din miljö.
* Läs [klientdokumentationen för Time Series Insights.](https://github.com/Microsoft/tsiclient)
