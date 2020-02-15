---
title: Samla in loggdata
titleSuffix: Azure Cognitive Search
description: Samla in och analysera loggdata genom att aktivera en diagnostisk inställning och Använd sedan Kusto-frågespråket för att utforska resultatet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 2849dc94f1c45dda3da09120adebba6e004eb96b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211183"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Samla in och analysera loggdata för Azure Kognitiv sökning

Diagnostik-eller drift loggar ger inblick i de detaljerade åtgärderna i Azure Kognitiv sökning och är användbara för övervakning av tjänst-och arbets belastnings processer. Internt finns loggar på Server delen under en kort tids period, tillräckligt för undersökning och analys om du filerar ett support ärende. Men om du vill använda självbetjäning för operativa data bör du konfigurera en diagnostisk inställning för att ange var loggnings information samlas in. 

Att konfigurera loggar är användbart för diagnostik och bevarande av drift historik. När du har aktiverat loggning kan du köra frågor eller skapa rapporter för strukturerad analys.

I följande tabell räknas alternativen för att samla in och bevara data.

| Resurs | Används för |
|----------|----------|
| [Skicka till Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Loggade händelser och fråga mått baserat på scheman nedan. Händelser loggas till en Log Analytics-arbetsyta. Med hjälp av Log Analytics kan du köra frågor för att returnera detaljerad information. Mer information finns i [Kom igång med Azure Monitor loggar](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Arkivera med Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Loggade händelser och fråga mått baserat på scheman nedan. Händelser loggas till en BLOB-behållare och lagras i JSON-filer. Loggarna kan vara ganska detaljerade (per timme/minut) som är användbara för att söka efter en speciell incident men inte för en öppen undersökning. Använd en JSON-redigerare för att visa en loggfil.|
| [Strömma till Händelsehubben](https://docs.microsoft.com/azure/event-hubs/) | Loggade händelser och fråga om mått baserat på de scheman som dokumenteras i den här artikeln. Välj det här som en alternativ data insamlings tjänst för mycket stora loggar. |

Både Azure Monitor-loggar och blob-lagring är tillgängliga som en kostnads fri tjänst så att du kan testa den utan kostnad för Azure-prenumerationens livs längd. Application Insights är kostnads fri att registrera dig och använda så länge program data storleken är under vissa gränser (se [sidan med priser](https://azure.microsoft.com/pricing/details/monitor/) för mer information).

## <a name="prerequisites"></a>Förutsättningar

Om du använder Log Analytics eller Azure Storage kan du skapa resurser i förväg.

+ [Skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) om du behöver ett logg arkiv.

## <a name="create-a-log"></a>Skapa en logg

Diagnostikinställningar definierar data insamling. En inställning anger hur och vad som samlas in. 

1. Under **övervakning**väljer du **diagnostikinställningar**.

   ![Diagnostikinställningar](./media/search-monitor-usage/diagnostic-settings.png "Diagnostikinställningar")

1. Välj **+ Lägg till diagnostisk inställning**

1. Välj de data som du vill exportera: loggar, mått eller båda. Du kan samla in data i ett lagrings konto, en Log Analytics-arbetsyta eller strömma det till Event Hub.

   Log Analytics rekommenderas eftersom du kan fråga arbets ytan i portalen.

   Om du också använder Blob Storage kommer behållare och blobbar att skapas som nödvändiga när loggdata exporteras.

   ![Konfigurera data insamling](./media/search-monitor-usage/configure-storage.png "Konfigurera data insamling")

1. Spara inställningen.

1. Testa genom att skapa eller ta bort objekt (skapar logg händelser) och genom att skicka frågor (genererar mått). 

I Blob Storage skapas behållare endast när det finns en aktivitet som ska loggas eller mätas. När data kopieras till ett lagrings konto formateras data som JSON och placeras i två behållare:

* Insights-logs-operationlogs: för search trafikloggar
* Insights-mått-pt1m: för mått

**Det tar en timme innan behållarna visas i Blob Storage. Det finns en BLOB, per timme, per behållare.**

Loggar arkiveras i varje timme där aktiviteten sker. Följande sökväg är ett exempel på en loggfil som skapats den 12 2020 januari kl. 9:00 var varje `/` är en mapp: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2020/m=01/d=12/h=09/m=00/name=PT1H.json`

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

## <a name="view-log-files"></a>Visa loggfiler

Blob Storage används för att arkivera loggfiler. Du kan Visa logg filen med valfri JSON-redigerare. Om du inte har en sådan rekommenderar vi [Visual Studio Code](https://code.visualstudio.com/download).

1. I Azure Portal öppnar du ditt lagrings konto. 

2. Klicka på **blobbar**i det vänstra navigerings fönstret. Du bör se **Insights-logs-operationlogs** och **Insights-Metrics-pt1m**. Dessa behållare skapas av Azure Kognitiv sökning när loggdata exporteras till Blob Storage.

3. Klicka på mapphierarkin tills du når. JSON-filen.  Använd snabb menyn för att hämta filen.

När filen har hämtats öppnar du den i en JSON-redigerare för att visa innehållet.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det kan du läsa grunderna i Sök tjänst övervakningen för att lära dig mer om alla funktioner för överblick.

> [!div class="nextstepaction"]
> [Övervaka åtgärder och aktiviteter i Azure Kognitiv sökning](search-monitor-usage.md)