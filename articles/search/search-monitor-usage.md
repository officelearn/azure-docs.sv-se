---
title: Övervaka användning och statistik för en search-tjänst – Azure Search
description: Spåra resursstorlek för förbrukning och index för Azure Search, en värdbaserad molnsöktjänst på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 584d1d8ce3285f9f5fb986c9779d3c403ce13d1b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314167"
---
# <a name="monitor-an-azure-search-service-in-azure-portal"></a>Övervaka en Azure Search-tjänst i Azure-portalen

Azure Search erbjuder olika resurser för att spåra användning och prestanda för search-tjänsterna. Den ger dig tillgång till mått, loggar, indexstatistiken och utökade övervakningsfunktioner på Power BI. Den här artikeln beskrivs hur du aktiverar de olika strategierna för övervakning och hur du tolkar resultatet.

## <a name="azure-search-metrics"></a>Azure Search-mått
Mått ger dig en insyn i din söktjänst realtid och är tillgängliga för varje tjänst, utan ytterligare inställningar. De kan du spåra prestanda för din tjänst i upp till 30 dagar.

Azure Search samlar in data till tre olika mått:

* Svarstid för sökning: Tid search-tjänst som behövs för att bearbeta sökfrågor, aggregerade per minut.
* Sökfrågor per sekund (QPS): Sök efter antalet frågor som tagits emot per sekund, aggregerade per minut.
* Begränsade sökfrågor i procent: Procentandel sökfrågor som har begränsats samman per minut.

![Skärmbild av Indexlagring aktivitet][1]

### <a name="set-up-alerts"></a>Ställa in aviseringar
Du kan konfigurera aviseringar för att utlösa ett e-postmeddelande eller en automatisk åtgärd när ett mått överskrider ett tröskelvärde som du har definierat mått detaljsidan.

Mer information om mått finns fullständig dokumentation om Azure Monitor.  

## <a name="how-to-track-resource-usage"></a>Hur du spårar användning
Spåra tillväxten av index och dokumentstorlek kan hjälpa dig att proaktivt justera kapacitet innan du når den övre gränsen som du har skapat för din tjänst. Du kan göra detta på portalen eller via programmering med hjälp av REST-API.

### <a name="using-the-portal"></a>Använda portalen

För att övervaka Resursanvändning, visa antalet och statistik för tjänsten i den [portal](https://portal.azure.com).

1. Logga in på [portalen](https://portal.azure.com).
2. Öppna instrumentpanelen för Azure Search-tjänsten. Paneler för tjänsten finns på startsidan eller du kan bläddra till tjänsten från Bläddra i Snabbåtkomstfältet.

Användning-avsnittet innehåller en mätare som anger vilken del av tillgängliga resurser är för närvarande används. Information om gränserna per tjänst för index, dokument och lagring finns i [tjänstbegränsningar](search-limits-quotas-capacity.md).

  ![Användningsikonen][2]

> [!NOTE]
> Skärmbilden ovan är för den kostnadsfria tjänsten som har högst en replik och partitionera varje och kan endast värden 3 index, 10 000 dokument eller 50 MB data, beroende på vilket som inträffar först. Tjänster som skapas vid en Basic eller Standard-nivån har mycket större tjänstbegränsningar. Läs mer om hur du väljer en nivå, [väljer en nivå eller SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Använda REST API
Både Azure Search REST API och .NET SDK ger Programmeringsåtkomst till mätvärden.  Om du använder [indexerare](https://msdn.microsoft.com/library/azure/dn946891.aspx) för att läsa in ett index från Azure SQL Database eller Azure Cosmos DB, en ytterligare API som används för att hämta de siffror som du behöver.

* [Hämta Indexstatistiken](/rest/api/searchservice/get-index-statistics)
* [Antal dokument](/rest/api/searchservice/count-documents)
* [Hämta Status för indexerare](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Hur du exporterar loggar och mått

Du kan exportera åtgärdsloggar för din tjänst och rådata för de mått som beskrivs i föregående avsnitt. Åtgärden loggar gör att du vet hur tjänsten används och kan användas från Power BI när data kopieras till ett lagringskonto. Azure search har ett övervakning Power BI-innehållspaket för detta ändamål.


### <a name="enabling-monitoring"></a>Aktivera övervakning
Öppna Azure Search-tjänsten i den [Azure-portalen](http://portal.azure.com) under alternativet Aktivera övervakning.

Välj de data som du vill exportera: Loggar, mått eller båda. Du kan kopiera den till ett lagringskonto, skickar den till en event hub eller exportera den till Log Analytics.

![Så här aktiverar du övervakning i portalen][3]

Om du vill aktivera med PowerShell eller Azure CLI finns i dokumentationen [här](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Loggar och mått scheman
När data kopieras till ett lagringskonto, formaterade data som JSON och dess plats i två behållare:

* Insights-logs-operationlogs: för search trafikloggar
* Insights-mått-pt1m: för mått

Det finns en blob, per timme per behållare.

Exempel på sökväg: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Log-schema
Loggar blobbarna innehåller dina trafikloggar för search-tjänsten.
Varje blob har en rotobjektet kallas **poster** som innehåller en matris med objekt i loggen.
Varje blob har poster på den åtgärd som utfördes under en och samma timme.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| time |datetime |”2015-12-07T00:00:43.6872559Z” |Tidsstämpel för åtgärden |
| resourceId |sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS-STANDARD-PROVIDERS /<br/> MICROSOFT. SÖK/SEARCHSERVICES/SEARCHSERVICE ” |Din resurs-ID |
| operationName |sträng |”Query.Search” |Åtgärdens namn |
| operationVersion |sträng |"2015-02-28" |Api-versionen som används |
| category |sträng |”OperationLogs” |konstant |
| resultType |sträng |”Lyckades” |Möjliga värden: Lyckad eller misslyckad |
| resultSignature |int |200 |Resultatkod för HTTP |
| . durationMS |int |50 |Varaktighet i millisekunder |
| properties |objekt |Se följande tabell |Objekt som innehåller åtgärden-specifika data |

**Egenskaper för schema**

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| Beskrivning |sträng |”Hämta /indexes('content')/docs” |Åtgärdens slutpunkt |
| Söka i data |sträng |”? Sök = AzureSearch & $count = true & api-version = 2015-02-28” |Frågeparametrar |
| Dokument |int |42 |Antal bearbetade dokument |
| Indexnamn |sträng |”testindex” |Namnet på det index som är associerade med åtgärden |

#### <a name="metrics-schema"></a>Mått-schema

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| resourceId |sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS-STANDARD-PROVIDERS /<br/>MICROSOFT. SÖK/SEARCHSERVICES/SEARCHSERVICE ” |resurs-id |
| MetricName |sträng |”Svarstiden” |namnet på måttet |
| time |datetime |”2015-12-07T00:00:43.6872559Z” |åtgärdens tidsstämpel |
| genomsnittligt |int |64 |Medelvärdet för raw-exempel i mått tidsintervallet |
| min |int |37 |Det lägsta värdet på raw exempel i mått tidsintervallet |
| max |int |78 |Det högsta värdet för raw-exempel i mått tidsintervallet |
| totalt |int |258 |Det totala värdet av rådata exempel i mått tidsintervallet |
| count |int |4 |Antalet raw exempel som används för att generera måttet |
| timegrain |sträng |”PT1M” |Tidsenhet för mått i ISO 8601 |

Alla mått rapporteras i minuts intervall. Varje måttet visas lägsta, högsta och genomsnittliga värden per minut.

För SearchQueriesPerSecond mått är minimivärdet det lägsta värdet för sökfrågor per sekund som registrerades under den minuten. Samma gäller för det högsta värdet. Genomsnittlig, är mängden för hela minuten.
Tänk på hur det här scenariot under en minut: en sekund hög läsa in det vill säga maximalt för SearchQueriesPerSecond, följt av 58 sekunders genomsnittliga belastningen, och slutligen en sekund med enbart en fråga som är minst.

För ThrottledSearchQueriesPercentage, lägsta, högsta, genomsnittlig och total, alla har samma värde: procentandelen sökfrågor som har begränsats från det totala antalet sökfrågor under en minut.

## <a name="analyzing-your-data-with-power-bi"></a>Analysera dina data med Power BI

Vi rekommenderar att du använder [Power BI](https://powerbi.microsoft.com) att utforska och visualisera dina data. Du kan enkelt ansluta den till ditt Azure Storage-konto och snabbt börja analysera dina data.

Azure Search har ett [Power BI-Innehållspaketet](https://app.powerbi.com/getdata/services/azure-search) som gör det möjligt att övervaka och förstå trafiken sökning med fördefinierade diagram och tabeller. Den innehåller en uppsättning Power BI-rapporter som automatiskt ansluter till dina data och ger visuella insikter om din söktjänst. Mer information finns i den [hjälpsidan för Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Power BI-instrumentpanel för Azure Search][4]

## <a name="next-steps"></a>Nästa steg
Granska [skala repliker och partitioner](search-limits-quotas-capacity.md) anvisningar om hur du balanserar fördelningen av partitionerna och replikerna för en befintlig tjänst.

Besök [hantera din söktjänst på Microsoft Azure](search-manage.md) för mer information om tjänstadministration, eller [prestanda och optimering](search-performance-optimization.md) för justering vägledning.

Lär dig mer om att skapa fantastiska rapporter. Se [komma igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) information

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
