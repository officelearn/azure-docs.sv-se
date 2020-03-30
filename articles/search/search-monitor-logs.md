---
title: Samla in loggdata
titleSuffix: Azure Cognitive Search
description: Samla in och analysera loggdata genom att aktivera en diagnostikinställning och använd sedan Kusto Query Language för att utforska resultat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462382"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Samla in och analysera loggdata för Azure Cognitive Search

Diagnostik- eller driftloggar ger insikt i detaljerade åtgärder för Azure Cognitive Search och är användbara för övervakning av tjänst- och arbetsbelastningsprocesser. Internt finns loggar på backend under en kort tid, tillräckligt för utredning och analys om du lämnar in en supportbiljett. Men om du vill ha självriktning över driftdata bör du konfigurera en diagnostikinställning för att ange var loggningsinformation samlas in.

Det är användbart att ställa in loggar för diagnostik och bevara drifthistoriken. När du har aktiverat loggning kan du köra frågor eller skapa rapporter för strukturerad analys.

I följande tabell räknas alternativ för insamling och beständighet av data.

| Resurs | Används för |
|----------|----------|
| [Skicka till Log Analytics-arbetsytan](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Händelser och mått skickas till en Log Analytics-arbetsyta, som kan efterfrågas i portalen för att returnera detaljerad information. En introduktion finns i [Komma igång med Azure Monitor-loggar](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Arkiv med Blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Händelser och mått arkiveras till en Blob-behållare och lagras i JSON-filer. Loggar kan vara ganska detaljerade (per timme / minut), användbart för att undersöka en specifik incident men inte för öppen undersökning. Använd en JSON-redigerare för att visa en rå loggfil eller Power BI för att sammanställa och visualisera loggdata.|
| [Strömma till händelsehubben](https://docs.microsoft.com/azure/event-hubs/) | Händelser och mått strömmas till en Azure Event Hubs-tjänst. Välj detta som en alternativ datainsamlingstjänst för mycket stora loggar. |

Både Azure Monitor-loggar och Blob-lagring är tillgängliga som en kostnadsfri tjänst så att du kan prova det utan kostnad under hela din Azure-prenumeration. Application Insights är gratis att registrera dig och använda så länge programdatastorleken är under vissa gränser (se [prissidan](https://azure.microsoft.com/pricing/details/monitor/) för mer information).

## <a name="prerequisites"></a>Krav

Om du använder Log Analytics eller Azure Storage kan du skapa resurser i förväg.

+ [Skapa en logganalysarbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [skapar ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Aktivera datainsamling

Diagnostikinställningar anger hur loggade händelser och mått samlas in.

1. Under **Övervakning** väljer du **Diagnostikinställningar**.

   ![Diagnostikinställningar](./media/search-monitor-usage/diagnostic-settings.png "Diagnostikinställningar")

1. Välj **+ Lägg till diagnostikinställning**

1. Kontrollera **Logganalys,** välj arbetsyta och välj **OperationLogs** och **AllMetrics**.

   ![Konfigurera datainsamling](./media/search-monitor-usage/configure-storage.png "Konfigurera datainsamling")

1. Spara inställningen.

1. När loggningen har aktiverats använder du söktjänsten för att börja generera loggar och mått. Det tar tid innan loggade händelser och mått blir tillgängliga.

För Log Analytics tar det flera minuter innan data är tillgängliga, varefter du kan köra Kusto-frågor för att returnera data. Mer information finns i [Övervaka frågebegäranden](search-monitor-logs.md).

För Blob-lagring tar det en timme innan behållarna visas i Blob-lagring. Det finns en blob, per timme, per behållare. Behållare skapas bara när det finns en aktivitet att logga eller mäta. När data kopieras till ett lagringskonto formateras data som JSON och placeras i två behållare:

+ insights-logs-operationlogs: för söktrafikloggar
+ insights-metrics-pt1m: för mått

## <a name="query-log-information"></a>Information om frågelogg

I diagnostikloggar innehåller två tabeller loggar och mått för Azure Cognitive Search: **AzureDiagnostics** och **AzureMetrics**.

1. Under **Övervakning**väljer du **Loggar**.

1. Ange **AzureMetrics** i frågefönstret. Kör den här enkla frågan för att bekanta dig med de data som samlas in i den här tabellen. Bläddra över tabellen för att visa mått och värden. Lägg märke till antalet poster högst upp, och om din tjänst har samlat in mått ett tag kanske du vill justera tidsintervallet för att få en hanterbar datauppsättning.

   ![AzureMetrics-tabellen](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics-tabellen")

1. Ange följande fråga för att returnera en tabellresultatuppsättning.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Upprepa föregående steg, som börjar med **AzureDiagnostics** för att returnera alla kolumner i informationssyfte, följt av en mer selektiv fråga som extraherar mer intressant information.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics-tabell](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics-tabell")

## <a name="log-schema"></a>Loggschema

Datastrukturer som innehåller Azure Cognitive Search-loggdata överensstämmer med schemat nedan. 

För Blob-lagring har varje blob ett rotobjekt som kallas **poster** som innehåller en matris med loggobjekt. Varje blob innehåller poster för alla åtgärder som ägde rum under samma timme.

Följande tabell är en ofullständig lista över fält som är gemensamma för diagnostikloggning.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| tidGenererad |datetime |"2018-12-07T00:00:43.6872559Z" |Tidsstämpel för åtgärden |
| resourceId |sträng |"/ABONNEMANG/1111111-1111-1111-1111-11111111111111/<br/>RESURSGRUPPER/DEFAULT/PROVIDERS/<br/> Microsoft. SÖK/SÖKTJÄNSTER/SÖKTJÄNST" |Din ResourceId |
| operationName |sträng |"Fråga.Sök" |Namnet på operationen |
| operationVersion |sträng |"2019-05-06" |Den api-version som används |
| category |sträng |"OperationLogs" |Konstant |
| resultType |sträng |"Framgång" |Möjliga värden: Framgång eller misslyckande |
| resultSignature |int |200 |HTTP-resultatkod |
| durationMS |int |50 |Varaktighet för operationen i millisekunder |
| properties |objekt |se följande tabell |Objekt som innehåller åtgärdsspecifika data |

### <a name="properties-schema"></a>Egenskapsschema

Egenskaperna nedan är specifika för Azure Cognitive Search.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| Description_s |sträng |"GET /indexes('content')/docs" |Åtgärdens slutpunkt |
| Documents_d |int |42 |Antal dokument som bearbetats |
| IndexName_s |sträng |"testindex" |Namn på det index som är associerat med operationen |
| Query_s |sträng |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Frågeparametrarna |

## <a name="metrics-schema"></a>Schema för mått

Mått samlas in för frågebegäranden och mäts i en minuts intervall. Varje mått visar lägsta, högsta och genomsnittliga värden per minut. Mer information finns i [Övervaka frågebegäranden](search-monitor-queries.md).

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| resourceId |sträng |"/ABONNEMANG/1111111-1111-1111-1111-11111111111111/<br/>RESURSGRUPPER/DEFAULT/PROVIDERS/<br/>Microsoft. SÖK/SÖKTJÄNSTER/SÖKTJÄNST" |ditt resurs-ID |
| metricName |sträng |"Latens" |namnet på måttet |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |operationens tidsstämpel |
| genomsnitt |int |64 |Det genomsnittliga värdet för råproverna i måtttidsintervallet, enheter i sekunder eller procent, beroende på måttet. |
| min |int |37 |Det minsta värdet av råproverna i det metriska tidsintervallet, enheter i sekunder. |
| max |int |78 |Det maximala värdet för råproverna i det metriska tidsintervallet, enheter i sekunder.  |
| totalt |int |258 |Det totala värdet av råproverna i det metriska tidsintervallet, enheter i sekunder.  |
| count |int |4 |Antalet mått som skickas från en nod till loggen inom ett minutintervall.  |
| tidsgrain |sträng |"PT1M" |Tidskornet för måttet i ISO 8601. |

Det är vanligt att frågor körs i millisekunder, så endast frågor som mäter som sekunder visas i mått som QPS.

För måttet **Sökfrågor per sekund** är lägsta värdet för sökfrågor per sekund som registrerades under den minuten. Detsamma gäller för det maximala värdet. Genomsnitt, är summan över hela minuten. Inom en minut kan du till exempel ha ett mönster som detta: en sekund av hög belastning som är den maximala för SearchQueriesPerSecond, följt av 58 sekunder av genomsnittlig belastning, och slutligen en sekund med bara en fråga, vilket är det minsta.

För **begränsade sökfrågor Procent**, minimum, maximum, genomsnitt och totalt har alla samma värde: procentandelen sökfrågor som begränsades, från det totala antalet sökfrågor under en minut.

## <a name="view-raw-log-files"></a>Visa råloggfiler

Blob-lagring används för arkivering av loggfiler. Du kan använda valfri JSON-redigerare för att visa loggfilen. Om du inte har någon rekommenderar vi [Visual Studio Code](https://code.visualstudio.com/download).

1. Öppna ditt Lagringskonto i Azure-portalen. 

2. Klicka på **Blobbar**i fönstret till vänster. Du bör se **insikter-logs-operationlogs** och **insights-metrics-pt1m**. Dessa behållare skapas av Azure Cognitive Search när loggdata exporteras till Blob-lagring.

3. Klicka nedåt i mapphierarkin tills du når .json-filen.  Använd snabbmenyn för att hämta filen.

När filen har hämtats öppnar du den i en JSON-redigerare för att visa innehållet.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det kan du läsa grunderna för övervakning av söktjänsten för att lära dig mer om hela utbudet av tillsynsfunktioner.

> [!div class="nextstepaction"]
> [Övervaka åtgärder och aktivitet i Azure Cognitive Search](search-monitor-usage.md)