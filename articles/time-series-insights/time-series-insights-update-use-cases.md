---
title: Förhandsgranska användnings fall – Azure Time Series Insights | Microsoft Docs
description: Läs mer om Azure Time Series Insights för hands versioner av användnings fall.
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
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087386"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights för hands versions användning

I den här artikeln sammanfattas flera vanliga användnings fall för Azure Time Series Insights för hands version. Rekommendationerna i den här artikeln fungerar som en utgångs punkt för att utveckla dina program och lösningar med Time Series Insights.

Mer specifikt besvarar den här artikeln följande frågor:

* Vad är vanliga användnings fall för Time Series Insights?
* Vilka är fördelarna med att använda Time Series Insights för [data utforskning och visuell avvikelse identifiering](#data-exploration-and-visual-anomaly-detection)?
* Vilka är fördelarna med att använda Time Series Insights för [drift analys och process effektivitet](#operational-analysis-and-driving-process-efficiency)?
* Vilka är fördelarna med att använda Time Series Insights för [Avancerad analys](#advanced-analytics)?

En översikt över dessa användnings scenarier beskrivs i följande avsnitt.

## <a name="introduction"></a>Inledning

Azure Time Series Insights är ett erbjudande från slut punkt till slut punkt, plattform-som-tjänst. Den används för att samla in, bearbeta, lagra, analysera och fråga mycket sammanhangsbaserade, tidsbaserad, tidsbaserad och optimerad IoT-Scale-data. Time Series Insights passar perfekt för ad hoc-datagranskning och operativ analys. Time Series Insights är ett unikt utöknings Bart, anpassat tjänst erbjudande som uppfyller de breda behoven i industriella IoT-distributioner.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Data utforskning och visuell avvikelse identifiering

Utforska och analysera miljard tals händelser för att upptäcka avvikelser och identifiera dolda trender i dina data. Time Series Insights ger prestanda i nära real tid för dina IoT-och DevOps analys arbets belastningar.

[![data Utforskaren](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

De flesta kunder samtycker till att den minsta tiden som krävs för att få insikter är en av främsta-funktionerna i Time Series Insights:

* Time Series Insights kräver ingen förberedelse av första data. 
* Det fungerar snabbt för att ansluta dig till miljarder händelser i Azure IoT Hub eller Azure Event Hubs-instanser på några minuter. 
* När du är ansluten kan du visualisera och analysera miljarder händelser för att upptäcka avvikelser och identifiera dolda trender i dina data.

Time Series Insights är intuitivt och enkelt att använda. Du kan interagera med dina data utan att behöva skriva en enda rad kod. Det finns inte heller något nytt språk som du behöver lära dig, men Time Series Insights tillhandahåller ett detaljerat textbaserade frågespråk för avancerade användare som är bekanta med SQL. Du kan också välja att välja och klicka på utforskning för nybörjare.

Kunder kan dra nytta av hastigheten för att snabbt diagnostisera problem med till gångs problem. De kan utföra DevOps analys för att komma till rotor saken till en bugg i en IoT-lösning. De kan också identifiera områden för att flagga för ytterligare undersökning som en del av deras initiativ för data vetenskap. 

Det finns tre huvudsakliga sätt att interagera med data som lagras i Time Series Insights:

* Det första och enklaste sättet att komma igång med Time Series Insights Preview Explorer. Du kan använda den för att snabbt visualisera alla dina IoT-data på ett och samma ställe. Den innehåller verktyg som värme kartan för att hjälpa dig att upptäcka avvikelser i dina data. Den innehåller också en perspektivvy. Använd den för att jämföra upp till fyra vyer från en eller flera Time Series Insights miljöer på en enda instrument panel. På instrument panelen får du en översikt över dina tids serie data över alla dina platser. Läs mer om [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md). Läs [Time Series Insights planera](./time-series-insights-update-plan.md)om du vill planera Time Series Insights miljön.

* Det andra sättet att starta är att använda JavaScript SDK för att snabbt bädda in kraftfulla diagram och grafer i ditt webb program. Med bara några rader med kod kan du skapa kraftfulla frågor. Använd dem för att fylla i linje diagram, cirkel diagram, liggande diagram, värme kartor, data rutnät med mera. Alla dessa element finns kvar i rutan med hjälp av SDK. SDK sammanfattar också Time Series Insights fråge-API: er. Du kan använda dem för att skapa SQL-liknande predikat för att fråga de data som du vill visa på en instrument panel. För Hybrid lösningar för presentations lager erbjuder Time Series Insights parametriserade URL: er. De ger sömlösa anslutnings punkter med Time Series Insights för hands versions Utforskaren för djup dykningar i data.

  * Läs om [klient biblioteket för Time Series Insights JS](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) och [Time Series Insights klient](https://github.com/Microsoft/tsiclient) dokumentationen om du vill veta mer om Java Script SDK.

  * Lär dig mer om delnings-URL: er och det nya användar gränssnittet genom att granska [visualisera data i Azure Time Series Insights Preview Explorer](time-series-insights-update-explorer.md).

* Det tredje sättet att starta är att använda de kraftfulla API: erna för att fråga data som lagras i Time Series Insights. Time Series Insights har temporala operatörer som `from`, `to`, `first`och `last`. Det innehåller agg regeringar och transformeringar som `average`, `min`, `max`, `split by`, `order by`och `DateHistogram`. Den har också filter operatörer som `has`, `in`, `and`, `or`, `greater than`och `REGEX`. Alla dessa operatörer gör det möjligt för underordnade program att snabbt hitta intressanta trender och mönster i dina data. Använd dem för att fylla i Homegrown-visualiseringar för att upptäcka avvikelser.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Drift analys och process effektivitet

Använd Time Series Insights för att övervaka hälsa, användning och prestanda för utrustning i stor skala. Time Series Insights är ett enkelt sätt att mäta drifts effektiviteten. Time Series Insights hjälper till att hantera olika och oförutsägbara IoT-arbetsbelastningar utan att offra inmatnings-eller frågans prestanda.

[Översikt över ![](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Strömning och kontinuerlig bearbetning av data som kommer från operativa processer kan omvandlas till alla företag om den kombineras med rätt teknik eller lösning. Dessa lösningar är ofta en kombination av flera system. De möjliggör utforskning och analys av data som ändras konstant, särskilt i IoT-sfären, och som delar ett gemensamt mönster.

Dessa mönster börjar ofta med IoT-aktiverade plattformar som matar in miljarder händelser från enheter och sensorer som sträcker sig över olika språk. Dessa system bearbetar och analyserar strömmande data för att få insikter och åtgärder i real tid. Data arkiveras vanligt vis i varmt och kall lagrings utrymme för nära real tids-och batch-analyser.

Data som samlas in går igenom en serie bearbetning för att rengöra och sätta den för efterföljande frågor och analys scenarier. Azure erbjuder omfattande tjänster som kan användas i IoT-scenarier som till exempel underhåll och tillverkning. Dessa tjänster omfattar Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning och Power BI.

Lösnings arkitektur kan uppnås på följande sätt:

* Mata in data via IoT Hub eller Event Hubs för förstklassig säkerhet, data flöde och svars tid.
* Utför data bearbetning och beräkningar. Tratt-inmatade data via tjänster som Stream Analytics, Logic Apps och Azure Functions. Vilken tjänst du använder beror på de olika data bearbetnings behoven.
* Beräknade signaler från bearbetnings pipelinen flyttas till Time Series Insights för lagring och analys.

Time Series Insights erbjuder data utforskning i nära real tid och till gångs rika insikter över historiska data. Beroende på dina affärs behov kan MapReduce och Hive-jobb köras på data som lagras i Time Series Insights genom att ansluta Time Series Insights till Azure HDInsight. Data som lagras i Time Series Insights är tillgängliga för Power BI och andra kund program via Time Series Insights API för offentlig Surface-fråga. Dessa data kan användas för djup affärs-och användnings informations scenarier.

## <a name="advanced-analytics"></a>Avancerad analys

Integrera med avancerade analys tjänster som Machine Learning och Azure Databricks. Time Series Insights ingress av rå data från miljon tals enheter. Den lägger till sammanhangsbaserade data som kan förbrukas sömlöst av en serie Azure Analytics-tjänster.

[![analys](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Avancerad analys och maskin inlärning förbrukar och bearbetar stora mängder data. Dessa data används för att fatta data drivna beslut och utföra förutsägelse analyser. I IoT-användningsfall lär sig avancerade analys algoritmer från data som samlas in från miljon tals enheter. Dessa enheter överför data flera gånger varje sekund. Data som samlas in från IoT-enheter är RAW. Den saknar sammanhangsbaserad information, till exempel enhetens plats och enhetens enhets läsning. Det innebär att rå data är svåra att förbruka direkt för avancerad analys.

Time Series Insights sammanfogar mellanrummet mellan IoT-data och avancerad analys på två enkla och kostnads effektiva sätt:

* Först Time Series Insights samlar data om rå telemetri från miljon tals enheter med hjälp av IoT Hub. Den berikar data med sammanhangsbaserad information och transformerar data i ett Parquet-format. Det här formatet kan enkelt integreras med andra avancerade analys tjänster, till exempel Machine Learning, Azure Databricks och program från tredje part.

    Time Series Insights kan fungera som källa för sanningen för alla data i en organisation. Den skapar en central lagrings plats för efterföljande analys arbets belastningar att använda. Eftersom Time Series Insights är en nära lagrings tjänst i real tid kan avancerade analys modeller lära sig kontinuerligt från inkommande IoT-telemetridata. Därför kan modeller göra mer exakta förutsägelser.

* Sedan kan du mata in utdata från modeller för maskin inlärning och förutsägelse i Time Series Insights för att visualisera och lagra resultatet. Den här proceduren hjälper organisationer att optimera och justera sina modeller. Time Series Insights gör det enkelt att visualisera data från strömmande telemetri på samma plan som de tränade modell utmatningarna. På så sätt hjälper det data vetenskaps team att upptäcka avvikelser och identifiera mönster.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
* Läs [Time Series Insights för hands versions planering](./time-series-insights-update-plan.md) för att planera för din miljö.
* Läs [Time Series Insights-klientens](https://github.com/Microsoft/tsiclient) dokumentation.
