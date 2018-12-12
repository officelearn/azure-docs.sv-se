---
title: Användningsfall för Azure Time Series Insights (förhandsversion) | Microsoft Docs
description: Förstå Azure Time Series Insights (förhandsversion) användningsfall
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 67be21ae7f0cb997563f17130b9d5ecb7d359b31
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873877"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Användningsfall för Azure Time Series Insights (förhandsversion)

Den här artikeln innehåller en översikt över flera vanliga användningsområden för Azure Time Series Insights (TSI). Rekommendationerna i den här artikeln fungerar som en startpunkt när du utvecklar program och lösningar med TSD.

När du har läst den här artikeln kommer du att kunna besvara följande frågor:

* Vad är vanliga användningsområden för Azure TSI?
* Vilka är fördelarna med att använda Azure TSI för datagranskning och visuell avvikelseidentifiering?
* Vilka är fördelarna med att använda Azure TSI för operativa analys och effektiviteten?
* Vilka är fördelarna med att använda Azure TSI för avancerade analyser?

Det här dokumentet innehåller en översikt över användningsfallen som Azure TSI Private Preview är avsedd för.

## <a name="introduction"></a>Introduktion

Azure TSI är en slutpunkt till slutpunkt Platform-As-A-Service att mata in, bearbeta, lagra och fråga efter data med mycket contextualized, tid-serien – optimerad IoT-skala. Därför är Azure TSI perfekt för ad hoc-datagranskning samt operativa analys. TSI är unikt utbyggbar och anpassade, tjänst, som uppfyller brett behöver industriella IoT-distributioner.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Datautforskning och visuell avvikelseidentifiering

Utforska och analysera direkt flera miljarder händelser för att upptäcka avvikelser och dolda trender i dina data. Azure TSI ger nästan i realtid prestanda för dina IoT- och DevOps analysis-arbetsbelastningar.

![data explorer][1]

För alla Azure TSI styrkor accepterar de flesta kunder att tiden till insikt är bland de starkaste. TSI kräver inga förberedande dataförberedelse och fungerar snabbt och ansluter till miljarder händelser i Azure IoT Hub eller Event Hub på några minuter.  När du är ansluten, kan du visualisera och analysera miljarder händelser för att upptäcka avvikelser och identifiera dolda trender i dina data.  Eftersom TSI är intuitivt och enkelt att använda kan börja du interagera med dina data utan att behöva skriva en enda rad kod. Du behöver inte heller lära dig något nytt språk. Time Series Insights tillhandahåller detaljerad, textbaserad frågekörning för avancerade användare som är bekanta med SQL samt ”välj och klicka”-förklaringar för nybörjare.

Vi kan se kunder att dra nytta av den här att diagnostisera tillgången-relaterade problem snabbt, utföra DevOps för att komma till de grundläggande orsakerna till en bugg i en IoT-lösning och för att identifiera områdena som ska undersökas efter data science initiativ.  

Det finns tre primära sätt att interagera med data som lagras i TSD:

1. Den första och enklast att komma igång med är vår visualiseringen i Utforskaren där du kan snabbt visualisera alla dina IoT-data på samma ställe. Den innehåller verktyg som den termiska kartan, vilket gör visuellt identifiera avvikelser i dina data enkelt. Det ger också perspektivvyn, vilka kan du jämföra upp till fyra vyer från en eller flera TSI-miljöer i en enda instrumentpanel, vilket ger dig en överblick över dina time series-data på alla dina platser. Läs mer om den [TSI-Utforskaren](./time-series-insights-update-explorer.md). Om du vill planera din miljö för uppdatering av TSI, läsa [TSI Uppdatera Planera](./time-series-insights-update-plan.md).

1. Det andra sättet är att använda vårt JavaScript SDK för att snabbt bädda in kraftfulla tabeller och diagram i dina egna webbprogram. Med bara några rader kod kan skapa du kraftfulla frågor för att fylla i linjediagram, cirkeldiagram, liggande diagram, termiska kartor, datarutnät och mer. Alla dessa element finns out-of the box med hjälp av SDK. SDK: N avlägsnar också TSI fråga API: er, så att du kan redigera SQL-liknande predikat att fråga efter data som du vill visa på en instrumentpanel. För presentationslagret hybridlösningar och erbjuder TSI parametriserade URL: er som ger sömlös kopplingspunkter med explorer för djup aktivitetsgruppsrapport i data. Mer information om JavaScript SDK i [TSI JS-klientbiblioteket](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) och [TSI klienten](https://github.com/Microsoft/tsiclient) dokumentation. Mer information om parametriserade URL: er kan du läsa vår artikel på [parametriserade URL: er](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).  

1. Slutligen innehåller TSI kraftfulla API: er för att fråga efter data som lagras i TSD. TSI har temporala operatorer som från, till, först och den sista aggregeringar och transformeringar som genomsnitt, min, max, dela med, order by- och DateHistogram och filtrering operatörer som har i och, eller större än REGEX osv. De här operatorerna aktivera applikationer nedströms för att snabbt hitta intressant trender och mönster i dina data och kan användas för att fylla i hemmagjorda visualiseringar för att upptäcka avvikelser.  

## <a name="operational-analysis-and-driving-process-efficiency"></a>Operativ analys och processeffektivitet

Aktivera övervakning av hälsotillstånd, användning och prestanda för utrustningen har i skala, vilket ger ett enkelt sätt att mäta effektiviteten. Time Series Insights hjälper till att hantera mångskiftande och oberäkneliga IoT-arbetsbelastningar utan att offra datainmatnings- eller frågeprestanda.

![översikt][2]

Strömning och kontinuerlig bearbetning av data som kommer från driftrutiner kan har omvandla alla företag om tillsammans med rätt tekniklösning. Dessa lösningar är ofta en kombination av flera system som gör det möjligt för undersökning och analys av data som ändras kontinuerligt särskilt i IoT-sfär och delar ett gemensamt mönster.

Dessa mönster börjar ofta med aktiverat IoT-plattformar som mata in miljontals händelser från enheter och sensorer som sträcker sig över olika språk. De här systemen bearbeta och analysera data för att härleda insikter i realtid och åtgärder, Data vanligtvis arkiveras varma och kalla lagring för nära realtid och batchanalyser. Data som samlas in går igenom serie bearbetning för att rensa och contextualize för underordnade frågor och analys. Microsoft Azure erbjuder omfattande tjänster som kan tillämpas på de här IoT-scenarier (tillgången underhåll, tillverkning, osv.). Dessa inkluderar Azure TSI, Azure IoT Hub, Azure Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning och Microsoft Power BI.

Den här lösningsarkitekturen kan ske enligt följande – mata in data via Azure IoT Hub eller Azure Event Hub för klassens bästa säkerhet, dataflöde och svarstid. Utföra bearbetning och beräkningar med stöd för funneling inmatade data via tjänster som Azure Stream Analytics, Azure Logic Apps, Azure Functions beroende bearbetning av specifika behov. Beräknad signaler från process-pipelinen skickas till Azure TSI för lagring och analys. Azure Time Series Insights erbjuder nästan i realtid datagranskning och tillgången insikter historiska data. Beroende på företagets behov, kan du köra MapReduce- och Hive-jobb på data som lagras i Time Series Insights genom att ansluta Time Series Insights till HDInsight. Data som lagras i Time Series Insights kan göras tillgängliga till Power BI och andra kundprogram via Time Series Insights offentliga surface fråga API: er för djupgående affärs- och driftsinformation scenarier.

## <a name="advanced-analytics"></a>Avancerad analys

Integrera med avancerad analys-tjänster som Azure Machine Learning och Azure Databricks. Time Series Insights för in rådata från flera miljoner enheter och lägger till sammanhangsbaserade data som kan förbrukas sömlöst med olika Azure-analystjänster.

![analys][3]

Avancerad analys och maskininlärning använda och bearbeta stora mängder data att fatta datadrivna beslut och utföra förutsägande analys. I IoT-användningsfall, Lär dig algoritmer för avancerad analys av data som samlas in från miljontals enheter som kan överföra data flera gånger per sekund. Men data som samlas in från IoT-enheter är rådata och saknar kontextinformation som platsen för enheten, enhet sensor avläsningen osv, vilket gör det svårt för data som ska användas direkt för avancerade analyser.

Azure Time Series Insights klyftan mellan IoT-data och avancerad analys på två enkelt och kostnadseffektivt sätt. Först Time Series Insights update samlar in råa telemetridata från miljontals enheter med hjälp av IoT hub, berikar data med sammanhangsberoende information och omvandlar data till ”parquet-format” som enkelt kan integrera med ett antal tjänster för avancerad analys, till exempel som Azure Machine Learning, Azure Databricks och andra program från tredje part.  Time Series Insights kan fungera som den källa av sanningen för alla data i en organisation, så skapa en central lagringsplats för underordnade analys arbetsbelastningar kan använda.  Eftersom Time Series Insights är en nästan i realtid storage-tjänsten, avancerad analys som modeller kan få kontinuerligt i inkommande telemetridata för IoT att göra mer exakta förutsägelser.

Andra kan Time Series Insights matas utdata för machine learning och förutsägande modeller att visualisera och lagra resultaten, vilket hjälper organisationer att optimera och justera sina modeller.  Dessutom gör Time Series Insights det enkelt att visualisera telemetridata på samma plan för direktuppspelning som den tränade modellen matar ut för att data datavetenskapsteam upptäcka avvikelser och identifiera mönster.  

## <a name="next-steps"></a>Nästa steg

Läs mer om den [TSI-Utforskaren](./time-series-insights-update-explorer.md).

Om du vill planera din miljö, läsa [TSI (förhandsversion) planera](./time-series-insights-update-plan.md).

Läs den [TSI klienten](https://github.com/Microsoft/tsiclient) dokumentation.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
