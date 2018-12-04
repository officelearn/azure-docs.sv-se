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
ms.date: 11/27/2018
ms.openlocfilehash: 9d9fab9f0a515cacdf2a1425c4da06c9e3d4c364
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52857001"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Användningsfall för Azure Time Series Insights (förhandsversion)

Den här artikeln innehåller en översikt över flera vanliga användningsområden för Azure Time Series Insights (TSI). Rekommendationerna i den här artikeln fungerar som en startpunkt när du utvecklar program och lösningar med TSD.

När du har läst den här artikeln kommer du att kunna besvara följande frågor:

* Vad är vanliga användningsområden för Azure TSI?
* Vilka är fördelarna med att använda Azure TSI för datagranskning och visuell avvikelseidentifiering?
* Vilka är fördelarna med att använda Azure TSI för operativa analys och effektiviteten?
* Vilka är fördelarna med att använda Azure TSI för avancerade analyser?

Det här dokumentet innehåller en översikt över användningsfallen som Azure Time Series Insights privat förhandsgranskning är utformad för.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Datagranskning och visuell avvikelseidentifiering

Utforska och analysera miljarder händelser för att upptäcka avvikelser och direkt identifiera dolda trender i dina data. Azure TSI ger nästan i realtid prestanda för dina IoT- och DevOps analysis-arbetsbelastningar.

![data explorer][1]

För alla Azure TSI styrkor accepterar de flesta kunder att tiden till insikt är bland de starkaste. TSI kräver inga förberedande dataförberedelse och fungerar snabbt och ansluter till miljarder händelser i Azure IoT Hub eller Event Hub på några minuter.  När du är ansluten, kan du visualisera och analysera miljarder händelser för att upptäcka avvikelser och identifiera dolda trender i dina data.  Eftersom TSI är intuitivt och enkelt att använda kan börja du interagera med dina data utan att behöva skriva en enda rad kod. Du behöver inte heller lära dig något nytt språk. Time Series Insights tillhandahåller detaljerad, textbaserad frågekörning för avancerade användare som är bekanta med SQL samt ”välj och klicka”-förklaringar för nybörjare.

Vi kan se kunder att dra nytta av den här att diagnostisera tillgången-relaterade problem snabbt, utföra DevOps för att komma till de grundläggande orsakerna till en bugg i en IoT-lösning och för att identifiera områdena som ska undersökas efter data science initiativ.  

Det finns tre primära sätt att interagera med data som lagras i TSD:

1. Den första och enklast att komma igång med är vår visualiseringen i Utforskaren där du kan snabbt visualisera alla dina IoT-data på samma ställe. Den tillhandahåller verktyg som den termiska kartan som gör visuellt identifiera avvikelser i dina data enkelt samt perspektivvyn, vilket gör att du jämför upp till fyra vyer från en eller flera TSI-miljöer i en enda instrumentpanel, vilket ger dig en överblick över dina time series-data mellan alla dina platser. Läs mer om den [TSI-Utforskaren](./time-series-insights-update-explorer.md). Om du vill planera din miljö för uppdatering av TSI, läsa [TSI Uppdatera Planera](./time-series-insights-update-plan.md).

1. Det andra sättet är att använda vårt JavaScript SDK för att snabbt bädda in kraftfulla tabeller och diagram i dina egna webbprogram. Med bara några rader kod kan skapa du kraftfulla frågor för att fylla i linjediagram, cirkeldiagram, liggande diagram, termiska kartor, datarutnät och mer. Alla dessa element finns out-of the box med hjälp av SDK. SDK: N avlägsnar också TSI fråga API: er, så att du kan redigera SQL-liknande predikat att fråga efter data som du vill visa på en instrumentpanel. För presentationslagret hybridlösningar och erbjuder TSI parametriserade URL: er som ger sömlös kopplingspunkter med explorer för djup aktivitetsgruppsrapport i data. Mer information om JavaScript SDK i [TSI JS-klientbiblioteket](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) och [TSI klienten](https://github.com/Microsoft/tsiclient) dokumentation. Mer information om parametriserade URL: er kan du läsa vår artikel på [parametriserade URL: er](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).  

1. Slutligen innehåller TSI kraftfulla API: er för att fråga efter data som lagras i TSD. TSI har temporala operatorer som från, till, först och den sista aggregeringar och transformeringar som genomsnitt, min, max, dela med, order by- och DateHistogram och filtrering operatörer som har i och, eller större än REGEX osv. De här operatorerna aktivera applikationer nedströms för att snabbt hitta intressant trender och mönster i dina data och kan användas för att fylla i hemmagjorda visualiseringar för att upptäcka avvikelser.  

Läs mer på Azure-erbjudanden för IoT, [skapa i Sakernas Internet](https://www.microsoft.com/internet-of-things).

## <a name="operational-analysis-and-driving-process-efficiency"></a>Operativa analys och utveckla maskinutnyttjande

Aktivera övervakning av hälsotillstånd, användning och prestanda för utrustningen har i skala, vilket ger ett enkelt sätt att mäta effektiviteten. Time Series Insights hjälper dig att hantera olika och oförutsägbara IoT-arbetsbelastningar utan att göra avkall på inmatning och frågeprestanda.

![översikt][2]

Strömning och kontinuerlig bearbetning av data som kommer från driftrutiner kan har omvandla alla företag om tillsammans med rätt tekniklösning. Dessa lösningar är ofta en kombination av flera system som gör det möjligt för undersökning och analys av data som ändras kontinuerligt särskilt i IoT-sfär. Dessa system tillsammans ljus upp de scenarier som delar ett gemensamt mönster när det gäller att mata in, bearbeta, lagra, analysera och visualisera IoT-data.

En del av lösningen, system behöver mata in miljontals händelser från enheter och sensorer som sträcker sig över olika språk. Sedan dessa system bearbeta och analysera data för att härleda insikter i realtid. Varma och kalla lagring för nära realtid och batchanalyser arkiveras sedan data.

Sedan måste dessa system du bearbeta data som samlas in om du vill aktivera rengöra och contextualization vid lagring av data för att aktivera scenarier med underordnade frågor och analys. Microsoft Azure erbjuder omfattande tjänster som kan användas för de här IoT-scenarier inklusive Azure TSI, Azure IoT Hub, Azure Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning och Microsoft Power BI.

Med inställningarna ovan lösning samlas data in via Azure IoT eller Event Hubs som den erbjuder hög genomströmning datainmatning med kort svarstid. Data som samlas in och som behöver bearbetas för insikter i realtid som kan vara går till Azure Stream Analytics, Azure Logic Apps och Azure Functions. Resultatet kan matas sedan till Power BI för i realtid dashboarding samt kan läsas in i Azure Time Series Insights för avisering och övervakning jämföra dem med historiska seeding. Data som samlas in och som behöver datautforskning i nära realtid eller ad hoc fråga efter historiska trender kan läsas in direkt till Azure Time Series Insights. Data läses in är redo att efterfrågas tillsammans med obegränsade historiska data för operativa analys och analyser för att optimera processer för maximal effektivitet. Alla data eller bara dataändringar i som lästs in av nyligen kan användas som för referensdata som en del av analys i realtid. Dessutom kan kan data ytterligare finjusteras och bearbetas genom att ansluta Azure Time Series Insights-data till HDInsight för Map/Reduce, Hive, osv. Slutligen göra den tillgänglig i Power BI och i alla kundprogram via vårt offentliga surface fråga API: er.

## <a name="advanced-analytics"></a>Avancerad analys

Integrera med avancerade Analystjänster som Azure Machine Learning och Azure Databricks. TSI ingresses rådata från miljontals enheter och lägger till kontextuella data som kan användas sömlöst av en uppsättning tjänster för Azure Analytics.

![analys][3]

Avancerad analys och maskininlärning använda och bearbeta stora mängder data att fatta datadrivna beslut och utföra förutsägande analys. I IoT-användningsfall, Lär dig algoritmer för avancerad analys av data som samlas in från miljontals enheter som kan överföra data flera gånger per sekund. Men data som samlas in från IoT-enheter är rådata och saknar kontextinformation som platsen för enheten, enhet sensor avläsningen osv. Dessa data kan inte användas direkt för avancerade analyser.

Azure TSI klyftan mellan IoT-data och avancerad analys på ett enkelt och kostnadseffektivt sätt. TSI samlar in råa telemetridata från miljontals enheter, berikar data med sammanhangsberoende information och omvandlar data till ”parquet-format” som enkelt kan integrera med ett antal Azure Advanced Analytics-tjänster som Azure Machine Learning, Azure DataBricks och dina egna program från tredje part. Avancerade modeller för textanalys kan lära dig kontinuerligt från inkommande telemetridata för IoT att göra mer exakta förutsägelser.

## <a name="next-steps"></a>Nästa steg

* Läs mer om den [TSI-Utforskaren](./time-series-insights-update-explorer.md).

* Om du vill planera din miljö, läsa [TSI (förhandsversion) planera](./time-series-insights-update-plan.md).

* Läs den [TSI klienten](https://github.com/Microsoft/tsiclient) dokumentation.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.png
[2]: media/v2-update-use-cases/overview.png
[3]: media/v2-update-use-cases/advanced-analytics.png
