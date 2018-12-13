---
title: Användningsfall för Azure Time Series Insights Preview | Microsoft Docs
description: Förstå Azure Time Series Insights Preview användningsfall.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: a6ee03197a78a75ca57d0ed049cdd6a8df8b2149
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272974"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights Preview-användningsfall

Den här artikeln innehåller en översikt över flera vanliga användningsområden för förhandsversionen av Azure Time Series Insights. Rekommendationerna i den här artikeln fungerar som en startpunkt när du utvecklar program och lösningar med Time Series Insights.

När du har läst den här artikeln kan du besvara följande frågor:

* Vad är vanliga användningsområden för Time Series Insights?
* Vilka är fördelarna med att använda Time Series Insights för datagranskning och visuell avvikelseidentifiering?
* Vilka är fördelarna med att använda Time Series Insights för operativa analys och effektiviteten?
* Vilka är fördelarna med att använda Time Series Insights för avancerade analyser?

Det här dokumentet innehåller en översikt över användningsfall om förhandsversionen av Azure Time Series Insights är utformat för.

## <a name="introduction"></a>Introduktion

Time Series Insights är ett erbjudande för slutpunkt till slutpunkt-plattform som tjänst. Den används för att mata in, bearbeta, lagra och fråga efter data med mycket contextualized, tid-serien – optimerad IoT-skala. Time Series Insights är perfekt för ad hoc-datagranskning och operativa analys. Time Series Insights är unikt utbyggbar och anpassade, tjänst, som uppfyller brett behöver industriella IoT-distributioner.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Datautforskning och visuell avvikelseidentifiering

Utforska och analysera direkt flera miljarder händelser för att upptäcka avvikelser och dolda trender i dina data. Time Series Insights ger nära realtid-prestanda till IoT- och DevOps-analysarbetsbelastningar.

![Datautforskaren][1]

De flesta kunder accepterar att tiden till insikt är bland de starkaste resurser för Time Series Insights. Time Series Insights kräver inga förberedande dataförberedelse. Den fungerar snabbt för att ansluta dig till miljarder händelser i Azure IoT Hub eller Azure Event Hubs på några minuter. När du är ansluten, kan du visualisera och analysera miljarder händelser för att upptäcka avvikelser och identifiera dolda trender i dina data. 

Time Series Insights är intuitivt och enkelt att använda. Du kan interagera med dina data utan att behöva skriva en enda rad kod. Det finns också lära dig något nytt språk. Time Series Insights tillhandahåller detaljerad textbaserad frågekörning för avancerade användare som är bekanta med SQL. Det ger också Välj och klicka utforskning för nybörjare.

Kunder dra nytta av hastigheten att diagnostisera tillgången-relaterade problem snabbt. De kan utföra DevOps till de grundläggande orsakerna till en bugg i en IoT-lösning. De kan också identifiera områden ska undersökas efter data science initiativ.  

Det finns tre primära sätt att interagera med data som lagras i Time Series Insights:

- Det är det första och enklaste sättet att komma igång med förhandsversionen av Time Series Insights explorer. Du kan använda den för att snabbt visualisera alla dina IoT-data på samma ställe. Den innehåller verktyg som den termiska kartan som hjälper dig att upptäcka avvikelser i dina data. Det kan du visa perspektiv. Du kan använda den för att jämföra upp till fyra vyer från en eller flera Time Series Insights-miljöer i en enda instrumentpanel. Instrumentpanelen ger dig en överblick över dina time series-data på alla dina platser. Läs mer om den [förhandsversionen av Time Series Insights explorer](./time-series-insights-update-explorer.md). Om du vill planera din Time Series Insights-miljö, läsa [Time Series Insights planera](./time-series-insights-update-plan.md).

- Det andra sättet att starta är att använda JavaScript SDK för att snabbt bädda in kraftfulla tabeller och diagram i ditt webbprogram. Med bara några rader kod kan skapa du kraftfulla frågor. Du kan använda dem för att fylla i linjediagram, cirkeldiagram, liggande diagram, termiska kartor, datarutnät och mer. Alla dessa element finns out-of the box med hjälp av SDK. SDK: N avlägsnar också API: er för Time Series Insights-fråga. Du kan använda dem för att skapa SQL-liknande predikat att fråga efter data som du vill visa på en instrumentpanel. Time Series Insights erbjuder hybridlösningar för presentationslagret parametriserade URL: er. De tillhandahåller sömlös kopplingspunkter med förhandsversionen av Time Series Insights explorer för djupdykningar i data. Mer information om JavaScript SDK i [Time Series Insights JS-klientbiblioteket](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) och [Time Series Insights klienten](https://github.com/Microsoft/tsiclient) dokumentation. Mer information om parametriserade URL: er läsa [parametriserade URL: er](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).

- Det tredje sättet att starta är att använda det kraftfulla API: er för att köra frågor mot data som lagras i Time Series Insights. Time Series Insights har temporala operatorer som från, till, första och sista. Den innehåller aggregeringar och transformeringar som genomsnittsvärden, min, max, dela med order by- och DateHistogram. Det har även filtrering operatörer som har, i och, eller större än och REGEX. De här operatorerna aktivera applikationer nedströms för att snabbt hitta intressant trender och mönster i dina data. Du kan använda dem för att fylla i hemmagjorda visualiseringar för att upptäcka avvikelser.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Operativ analys och processeffektivitet

Använd Time Series Insights om du vill övervaka hälsotillstånd, användning och prestanda för utrustning i stor skala. Time Series Insights ger ett enkelt sätt att mäta effektiviteten. Time Series Insights hjälper till att hantera mångskiftande och oberäkneliga IoT-arbetsbelastningar utan att offra datainmatnings- eller frågeprestanda.

![Översikt][2]

Strömning och kontinuerlig bearbetning av data som kommer från driftrutiner kan har omvandla vilket företag som om den kombineras med rätt teknik eller lösning. Dessa lösningar är ofta en kombination av flera system. De gör undersökning och analys av data som ändras hela tiden, särskilt i IoT-området och delar ett gemensamt mönster.

Dessa mönster börjar ofta med IoT-baserade plattformar som mata in miljontals händelser från enheter och sensorer som sträcker sig över olika språk. De här systemen bearbeta och analysera data för att härleda insikter i realtid och åtgärder. Data är vanligtvis arkiveras för varm och kall lagring för nära realtid och batch-analys. 

Data som samlas in går igenom en serie bearbetning för att rensa och contextualize för underordnade frågor och analys. Azure erbjuder omfattande tjänster som kan tillämpas på IoT-scenarier, till exempel tillgången underhåll och tillverkning. Exempel med Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning och Power BI.

Lösningsarkitektur kan ske på följande sätt:

- Mata in data via IoT Hub eller Event Hubs för klassens bästa säkerhet, dataflöde och svarstid. 
- Utför bearbetning och beräkningar. Tratt inmatade data via tjänster som Stream Analytics, Logic Apps och Azure Functions. Tjänsten du använder beror på de specifika behoven för databearbetning. 
- Beräknad signaler från process-pipelinen skickas till Time Series Insights för att lagra och analys. 

Time Series Insights erbjuder nästan i realtid datagranskning och tillgången insikter historiska data. Beroende på företagets behov, kan MapReduce- och Hive-jobb köras på data som lagras i Time Series Insights genom att ansluta Time Series Insights till Azure HDInsight. Data som lagras i Time Series Insights är tillgänglig för Power BI och andra kundprogram via Time Series Insights offentliga surface frågan API: er. Dessa data kan användas för djupgående affärs- och driftsinformation scenarier.

## <a name="advanced-analytics"></a>Avancerad analys

Integrera med avancerad analys-tjänster som Machine Learning och Azure Databricks. Time Series Insights ingresses rådata från miljontals enheter. Det lägger till kontextuella data som kan användas sömlöst av en uppsättning tjänster för Azure analys.

![Analytics][3]

Avancerad analys och maskininlärning använda och bearbeta stora mängder data. Dessa data används för att fatta datadrivna beslut och utföra förutsägande analys. I IoT-användningsfall, Lär dig algoritmer för avancerad analys av data som samlas in från miljontals enheter. Dessa enheter överföra data flera gånger per sekund. Data som samlas in från IoT-enheter är rådata. Sammanhangsbaserad information som platsen för enheten och enheten sensor läsning saknar. Därför är det svårt att använda direkt för avancerad analys med rådata.

Time Series Insights klyftan mellan IoT-data och avancerad analys på två enkelt och kostnadseffektivt sätt: 

- Först Time Series Insights samlar in råa telemetridata från miljontals enheter med hjälp av IoT Hub. Den får data med sammanhangsberoende information och omvandlar data till en parquet-format. Det här formatet kan enkelt integreras med andra Avancerade analysverktyg tjänster, till exempel Machine Learning, Azure Databricks och program från tredje part. 

    Time Series Insights kan fungera som källa till sanningen för alla data i en organisation. Den skapar en central lagringsplats för underordnade analys arbetsbelastningar kan använda. Eftersom Time Series Insights är en nästan i realtid storage service, avancerad analys som modeller kan få kontinuerligt i inkommande telemetridata för IoT. Modellerna kan därmed göra mer exakta förutsägelser.

- Time Series Insights kan dessutom matas utdata för machine learning och förutsägande modeller att visualisera och lagra sina resultat. Den här proceduren hjälper organisationer att optimera och justera sina modeller. Time Series Insights gör det enkelt att visualisera streaming telemetridata på samma plan som matar ut den tränade modellen. På så sätt kan hjälper den data science upptäcka avvikelser och identifiera mönster.  

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [förhandsversionen av Time Series Insights explorer](./time-series-insights-update-explorer.md).
- Om du vill planera din miljö, läsa [Time Series Insights Preview planera](./time-series-insights-update-plan.md).
- Läs den [Time Series Insights klienten](https://github.com/Microsoft/tsiclient) dokumentation.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
