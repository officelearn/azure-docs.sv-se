---
title: Samla in loggdata
titleSuffix: Azure Cognitive Search
description: Samla in och analysera loggdata genom att aktivera en diagnostisk inställning och Använd sedan Kusto-frågespråket för att utforska resultatet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462382"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Samla in och analysera loggdata för Azure Kognitiv sökning

Diagnostik-eller drift loggar ger inblick i de detaljerade åtgärderna i Azure Kognitiv sökning och är användbara för övervakning av tjänst-och arbets belastnings processer. Internt finns loggar på Server delen under en kort tids period, tillräckligt för undersökning och analys om du filerar ett support ärende. Men om du vill använda självbetjäning för operativa data bör du konfigurera en diagnostisk inställning för att ange var loggnings information samlas in.

Att konfigurera loggar är användbart för diagnostik och bevarande av drift historik. När du har aktiverat loggning kan du köra frågor eller skapa rapporter för strukturerad analys.

I följande tabell räknas alternativen för att samla in och bevara data.

| Resurs | Används för |
|----------|----------|
| [Skicka till Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Händelser och mått skickas till en Log Analytics-arbetsyta, som kan frågas i portalen för att returnera detaljerad information. En introduktion finns i [Kom igång med Azure Monitor loggar](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Arkivera med Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Händelser och mått arkiveras i en BLOB-behållare och lagras i JSON-filer. Loggarna kan vara ganska detaljerade (per timme/minut) som är användbara för att söka efter en speciell incident men inte för en öppen undersökning. Använd en JSON-redigerare för att visa en rå logg fil eller Power BI för att sammanställa och visualisera loggdata.|
| [Strömma till Händelsehubben](https://docs.microsoft.com/azure/event-hubs/) | Händelser och mät värden strömmas till en Azure Event Hubs-tjänst. Välj det här som en alternativ data insamlings tjänst för mycket stora loggar. |

Både Azure Monitor-loggar och blob-lagring är tillgängliga som en kostnads fri tjänst så att du kan testa den utan kostnad för Azure-prenumerationens livs längd. Application Insights är kostnads fri att registrera dig och använda så länge program data storleken är under vissa gränser (se [sidan med priser](https://azure.microsoft.com/pricing/details/monitor/) för mer information).

## <a name="prerequisites"></a>Förutsättningar

Om du använder Log Analytics eller Azure Storage kan du skapa resurser i förväg.

+ [Skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Aktivera datainsamling

Diagnostikinställningar anger hur loggade händelser och mått samlas in.

1. Under **Övervakning** väljer du **Diagnostikinställningar**.

   ![Diagnostikinställningar](./media/search-monitor-usage/diagnostic-settings.png "Diagnostikinställningar")

1. Välj **+ Lägg till diagnostisk inställning**

1. Markera **Log Analytics**, välj din arbets yta och välj **OperationLogs** och **AllMetrics**.

   ![Konfigurera data insamling](./media/search-monitor-usage/configure-storage.png "Konfigurera data insamling")

1. Spara inställningen.

1. När loggning har Aktiver ATS använder du Sök tjänsten för att börja generera loggar och mått. Det tar tid innan loggade händelser och mått blir tillgängliga.

För Log Analytics kommer det att vara flera minuter innan data är tillgängliga, och du kan köra Kusto-frågor för att returnera data. Mer information finns i [övervaka fråge förfrågningar](search-monitor-logs.md).

För Blob Storage tar det en timme innan behållarna visas i Blob Storage. Det finns en blob, per timme per behållare. Behållare skapas endast när det finns en aktivitet som ska loggas eller mätas. När data kopieras till ett lagrings konto formateras data som JSON och placeras i två behållare:

+ Insights-logs-operationlogs: för search trafikloggar
+ Insights-mått-pt1m: för mått

## <a name="query-log-information"></a>Fråga logg information

I diagnostiska loggar innehåller två tabeller loggar och mått för Azure Kognitiv sökning: **AzureDiagnostics** och **AzureMetrics**.

1. Under **övervakning**väljer du **loggar**.

1. Ange **AzureMetrics** i frågefönstret. Kör den här enkla frågan för att bekanta dig med de data som samlas in i den här tabellen. Bläddra i tabellen om du vill visa mått och värden. Observera antalet poster överst, och om tjänsten har samlat in måtten för ett tag kan du behöva justera tidsintervallet för att få en hanterbar data uppsättning.

   ![AzureMetrics-tabell](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics-tabell")

1. Ange följande fråga för att returnera en tabell resultat uppsättning.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Upprepa föregående steg, som börjar med **AzureDiagnostics** , för att returnera alla kolumner i informations syfte, följt av en mer selektiv fråga som hämtar mer intressant information.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics-tabell](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics-tabell")

## <a name="log-schema"></a>Log-schema

Data strukturer som innehåller Azure Kognitiv sökning loggdata följer schemat nedan. 

För Blob Storage har varje BLOB ett rot objekt som kallas **poster** som innehåller en matris med logg objekt. Varje Blob innehåller poster för alla åtgärder som ägde rum under samma timme.

Följande tabell är en del av listan över fält som är vanliga för diagnostisk loggning.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559Z" |Tidsstämpel för åtgärden |
| resourceId |sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS-STANDARD-PROVIDERS /<br/> MICROSOFT. SÖK/SEARCHSERVICES/SEARCHSERVICE ” |Din resurs-ID |
| operationName |sträng |”Query.Search” |Åtgärdens namn |
| operationVersion |sträng |"2019-05-06" |Api-versionen som används |
| category |sträng |”OperationLogs” |konstant |
| resultType |sträng |”Lyckades” |Möjliga värden: lyckad eller misslyckad |
| resultSignature |int |200 |Resultatkod för HTTP |
| . durationMS |int |50 |Varaktighet i millisekunder |
| properties |objekt |Se följande tabell |Objekt som innehåller åtgärden-specifika data |

### <a name="properties-schema"></a>Egenskaps schema

Egenskaperna nedan är bara för Azure-Kognitiv sökning.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| Description_s |sträng |”Hämta /indexes('content')/docs” |Åtgärdens slutpunkt |
| Documents_d |int |42 |Antal bearbetade dokument |
| IndexName_s |sträng |"test-index" |Namnet på det index som är associerade med åtgärden |
| Query_s |sträng |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Frågeparametrar |

## <a name="metrics-schema"></a>Mått-schema

Måtten fångas för fråge förfrågningar och mäts inom en minut. Varje måttet visas lägsta, högsta och genomsnittliga värden per minut. Mer information finns i [övervaka fråge förfrågningar](search-monitor-queries.md).

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| resourceId |sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS-STANDARD-PROVIDERS /<br/>MICROSOFT. SÖK/SEARCHSERVICES/SEARCHSERVICE ” |resurs-ID |
| metricName |sträng |”Svarstiden” |namnet på måttet |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |åtgärdens tidsstämpel |
| genomsnittligt |int |64 |Medelvärdet för RAW-exemplen i mått tidsintervallet, enheter i sekunder eller procent, beroende på måttet. |
| min |int |37 |Det minsta värdet för RAW-exemplen i Metric-tidsintervallet, enheter i sekunder. |
| max |int |78 |Det maximala värdet för RAW-exemplen i Metric-tidsintervallet, enheter i sekunder.  |
| totalt |int |258 |Det totala värdet för RAW-exemplen i Metric-tidsintervallet, enheter i sekunder.  |
| count |int |4 |Antalet mått som har avsänts från en nod till loggen inom ett minut intervall.  |
| timegrain |sträng |”PT1M” |Tids kornig het för måttet i ISO 8601. |

Det är vanligt att frågor körs i millisekunder, så endast frågor som mäter hur många sekunder visas i mått som frågor per sekund.

För **Sök frågor per sekund** -mått är minimivärdet det lägsta värdet för Sök frågor per sekund som registrerades under den minuten. Samma gäller för det högsta värdet. Genomsnittlig, är mängden för hela minuten. Inom en minut kan du till exempel ha ett mönster som liknar detta: en sekund med hög belastning som är max för SearchQueriesPerSecond, följt av 58 sekunders genomsnittlig belastning och slutligen en sekund med bara en fråga, vilket är minimivärdet.

För **begränsade Sök frågor i procent**, lägsta, högsta, genomsnitt och total, har alla samma värde: procent andelen Sök frågor som har begränsats från det totala antalet Sök frågor under en minut.

## <a name="view-raw-log-files"></a>Visa obehandlade loggfiler

Blob Storage används för att arkivera loggfiler. Du kan Visa logg filen med valfri JSON-redigerare. Om du inte har en sådan rekommenderar vi [Visual Studio Code](https://code.visualstudio.com/download).

1. I Azure Portal öppnar du ditt lagrings konto. 

2. Klicka på **blobbar**i det vänstra navigerings fönstret. Du bör se **Insights-logs-operationlogs** och **Insights-Metrics-pt1m**. Dessa behållare skapas av Azure Kognitiv sökning när loggdata exporteras till Blob Storage.

3. Klicka på mapphierarkin tills du når. JSON-filen.  Använd snabb menyn för att hämta filen.

När filen har hämtats öppnar du den i en JSON-redigerare för att visa innehållet.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det kan du läsa grunderna i Sök tjänst övervakningen för att lära dig mer om alla funktioner för överblick.

> [!div class="nextstepaction"]
> [Övervaka åtgärder och aktiviteter i Azure Kognitiv sökning](search-monitor-usage.md)