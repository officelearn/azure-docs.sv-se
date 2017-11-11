---
title: "Övervaka användning och statistik i en Azure Search-tjänst | Microsoft Docs"
description: "Spåra resursstorlek för användnings- och index för Azure Search värdbaserade moln search-tjänsten på Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: fe852afedfc1cce99d81b8ab53c6c80df34ac6d6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="monitoring-an-azure-search-service"></a>Övervaka en Azure Search-tjänst

Azure Search har olika resurser för att spåra användning och prestanda för search-tjänster. Det ger dig tillgång till mätvärden, loggar, indexstatistik och utökade övervakningsfunktioner på Power BI. Den här artikeln beskriver hur du aktiverar olika övervakning strategier och hur du tolkar resulterande data.

## <a name="azure-search-metrics"></a>Azure Search-mått
Mått får du nära realtid insyn i din söktjänst och är tillgängliga för varje tjänst utan ytterligare inställningar. De kan du spåra prestandan för din tjänst för upp till 30 dagar.

Azure Search samlar in data för tre olika mått:

* Sök svarstid: tid söktjänsten som behövs för att bearbeta sökfrågor samman per minut.
* Sök frågor per sekund (QPS): Sök antalet frågor som tagits emot per sekund, samman per minut.
* Begränsad sökning frågar procentandel: procentandelen av sökfrågor som har begränsats samman per minut.

![Skärmbild av QPS aktivitet][1]

### <a name="set-up-alerts"></a>Konfigurera aviseringar
Du kan konfigurera aviseringar för att utlösa ett e-postmeddelande eller en automatisk åtgärd när en måttet överskrider ett tröskelvärde som du har definierat mått detaljsida.

Mer information om mått fullständig dokumentationen på Azure-Monitor.  

## <a name="how-to-track-resource-usage"></a>Hur du spårar Resursanvändning
Spåra tillväxt index och storlek kan hjälpa dig att justera kapacitet innan träffa den övre gränsen som du skapade för din tjänst. Du kan göra detta på portalen eller programmässigt med hjälp av REST-API.

### <a name="using-the-portal"></a>Använda portalen

Om du vill övervaka Resursanvändning, visa antal och statistik för tjänsten i den [portal](https://portal.azure.com).

1. Logga in på den [portal](https://portal.azure.com).
2. Öppna instrumentpanelen för Azure Search-tjänsten. Du kan bläddra till tjänsten från Bläddra på JumpBar eller paneler för tjänsten kan hittas på startsidan.

Användning-avsnittet innehåller en mätare som anger vilken del av tillgängliga resurser används för närvarande. Mer information om per service gränser för index, dokument och lagring finns [gränser](search-limits-quotas-capacity.md).

  ![Ikonen användning][2]

> [!NOTE]
> Skärmbilden ovan för den kostnadsfria tjänsten som har högst en replik och partitionen varje och kan endast värden 3 index, 10 000 dokument eller 50 MB data, beroende på vilket som inträffar först. Tjänster som skapades på en Basic eller Standard nivå har mycket större tjänstbegränsningarna. Mer information om hur du väljer en nivå finns [väljer du en nivå eller SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Med hjälp av REST-API
Både Azure Search REST-API och .NET SDK ger programmatisk åtkomst till tjänsten mått.  Om du använder [indexerare](https://msdn.microsoft.com/library/azure/dn946891.aspx) för att läsa in ett index från Azure SQL Database eller Azure Cosmos DB en ytterligare API används för att hämta nummer som du behöver.

* [Få indexstatistik](/rest/api/searchservice/get-index-statistics)
* [Antal dokument](/rest/api/searchservice/count-documents)
* [Hämta Status för indexerare](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Hur du exporterar loggar och mått

Du kan exportera åtgärden loggarna för din tjänst och rådata för de mätvärden som beskrivs i föregående avsnitt. Åtgärden loggar gör att du vet hur tjänsten används och kan användas från Power BI när data kopieras till ett lagringskonto. Azure search innehåller en övervakning Power BI-Innehållspaketet för detta ändamål.


### <a name="enabling-monitoring"></a>Aktivera övervakning
Öppna Azure Search-tjänst i den [Azure-portalen](http://portal.azure.com) under alternativet Aktivera övervakning.

Välj de data som du vill exportera: loggar, mått eller båda. Du kan kopiera den till ett lagringskonto, skicka den till en händelsehubb eller exportera den till logganalys.

![Så här aktiverar du övervakning i portalen][3]

Om du vill aktivera med hjälp av PowerShell eller Azure CLI finns i dokumentationen för [här](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Loggar och mått scheman
När data kopieras till ett lagringskonto formateras data som JSON och dess plats i två behållare:

* insikter-loggar-operationlogs: för loggar med webbtrafik
* insikter-mått-pt1m: för mått

Det finns en blob, per timme per behållare.

Exempelsökväg till:`resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Loggen schema
Loggar blobbar innehålla dina trafikloggar för search-tjänsten.
Varje blobb har en rotobjektet kallas **poster** som innehåller en matris med objekt.
Varje blobb innehåller poster på den åtgärd som utfördes under samma timme.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| time |Datum och tid |”2015-12-07T00:00:43.6872559Z” |Tidsstämpel för åtgärden |
| resourceId |Sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESURSGRUPPER-STANDARD-PROVIDERS /<br/> MICROSOFT. SEARCHSERVICE-SÖKNINGEN/SEARCHSERVICES ” |Din ResourceId |
| operationName |Sträng |”Query.Search” |Namnet på åtgärden |
| operationVersion |Sträng |"2015-02-28" |Api-version som används |
| category |Sträng |”OperationLogs” |konstant |
| resultType |Sträng |”Lyckades” |Möjliga värden: lyckats eller misslyckats |
| resultSignature |int |200 |Resultatkod för HTTP |
| durationMS |int |50 |Varaktighet för åtgärden i millisekunder |
| properties |Objektet |Se tabellen nedan |Objekt som innehåller åtgärden-specifika data |

**Egenskaper för schemat**
| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| Beskrivning |Sträng |”Hämta /indexes('content')/docs” |Åtgärdens slutpunkt |
| Fråga |Sträng |”? Sök = AzureSearch & $count = true & api-version 2015-02-28 =” |Frågeparametrar |
| Dokument |int |42 |Antal bearbetade dokument |
| Indexnamn |Sträng |”testindex” |Namnet på det index som associeras med operationen |

#### <a name="metrics-schema"></a>Mått schema
| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| resourceId |Sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESURSGRUPPER-STANDARD-PROVIDERS /<br/>MICROSOFT. SEARCHSERVICE-SÖKNINGEN/SEARCHSERVICES ” |resurs-id |
| metricName |Sträng |”Latens” |namnet på måttet |
| time |Datum och tid |”2015-12-07T00:00:43.6872559Z” |åtgärdens tidsstämpel |
| Genomsnittlig |int |64 |Medelvärdet för raw exempel i mått tidsintervallet |
| minsta |int |37 |Det lägsta värdet för raw exempel i mått tidsintervallet |
| Maximalt |int |78 |Det maximala värdet för raw exempel i mått tidsintervallet |
| Totalt |int |258 |Det totala värdet av rådata exempel i mått tidsintervallet |
| Antal |int |4 |Antalet rådata exempel som används för att skapa måttet |
| tidskorn |Sträng |”PT1M” |Tidskornet på måttet i ISO 8601 |

Alla mätvärden rapporteras i minuts intervall. Varje måttet visas lägsta, högsta och genomsnittliga värden per minut.

För SearchQueriesPerSecond-mått är minst det lägsta värdet för sökfrågor per sekund som har registrerats under den minuten. Detsamma gäller för det högsta värdet. Genomsnittlig, är mängden över hela minut.
Tänk på hur det här scenariot under en minut: en andra hög läsa in som är maximalt för SearchQueriesPerSecond, följt av 58 sekunders genomsnittlig belastning och slutligen en sekund med enbart en fråga som är minst.

För ThrottledSearchQueriesPercentage, lägsta, högsta, genomsnittlig och totala, alla har samma värde: procentandelen sökfrågor som har begränsats från det totala antalet sökfrågor under en minut.

## <a name="analyzing-your-data-with-power-bi"></a>Analysera dina data med Power BI

Vi rekommenderar att du använder [Power BI](https://powerbi.microsoft.com) att utforska och visualisera dina data. Du kan enkelt ansluta den till Azure Storage-konto och snabbt börja analysera dina data.

Azure Search ger en [Power BI-Innehållspaketet](https://app.powerbi.com/getdata/services/azure-search) där du kan övervaka och förstå din sökning trafik med fördefinierade diagram och tabeller. Den innehåller en uppsättning Power BI-rapporter som automatiskt ansluter till dina data och ger visual inblick i din söktjänst. Mer information finns i [Innehållspaketet hjälpsidan](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Power BI-instrumentpanel för Azure Search][4]

## <a name="next-steps"></a>Nästa steg
Granska [skala repliker och partitioner](search-limits-quotas-capacity.md) för anvisningar om hur du balanserar tilldelningen partitioner och repliker för en befintlig tjänst.

Besök [hantera din söktjänst i Microsoft Azure](search-manage.md) mer information om tjänsten administration eller [prestanda och optimering](search-performance-optimization.md) för justering vägledning.

Mer information om hur du skapar häpnadsväckande rapporter. Se [komma igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) information

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
