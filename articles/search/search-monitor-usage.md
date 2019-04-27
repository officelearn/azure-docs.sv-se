---
title: Övervaka användning och fråga Resursmått för en search-tjänst – Azure Search
description: Aktivera loggning, få frågan aktivitetsmått, Resursanvändning och andra systemdata från en Azure Search-tjänst.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f4a0cba18f27c9cabfc03d1934469e6899c5cd18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564790"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Övervaka resource förbrukning och fråga i Azure Search

På sidan Översikt i Azure Search-tjänsten kan du visa systemdata om resursanvändning, fråga mått och hur mycket kvoten är att skapa flera index, indexerare och datakällor. Du kan också använda portalen för att konfigurera log analytics eller en annan resurs som används vid insamling av beständiga data. 

Konfigurera loggar är användbart för self diagnostik- och preserving operativa historik. Internt, finns loggar på serverdelen för en kort tidsperiod, räcker för undersökning och analys om en supportbegäran. Om du vill kontroll över- och att logga information, bör du ställa in en av de lösningar som beskrivs i den här artikeln.

I den här artikeln lär du dig om hur du övervakar alternativ, hur du aktiverar loggning och logga storage och hur du visar logginnehållet.

## <a name="metrics-at-a-glance"></a>Mått direkt

**Användning** och **övervakning** avsnitt som är inbyggda i översikten sidan rapporten ut i resursförbrukning och frågar om mått för körning. Den här informationen blir tillgänglig när du börjar använda tjänsten, med krävs ingen konfiguration. Den här sidan uppdateras några minuters mellanrum. Om du slutför beslut om [vilken nivå ska användas för produktionsarbetsbelastningar](search-sku-tier.md), eller om du vill [justera antalet aktiva repliker och partitioner](search-capacity-planning.md), de här måtten kan hjälpa dig med dessa beslut där du kan se hur snabbt resurser används och hur väl den aktuella konfigurationen hanterar befintlig belastning.

Den **användning** fliken visar resurstillgänglighet i förhållande till aktuell [gränser](search-limits-quotas-capacity.md). Följande bild är för den kostnadsfria tjänsten som är högst 3 objekt av varje typ och 50 MB lagringsutrymme. Basic eller Standard-tjänsten har högre gränser, och om du ökar antalet för partition, det största lagringsutrymmet går upp proportionellt.

![Status för användning i förhållande till effektiva gränser](./media/search-monitor-usage/usage-tab.png
 "för programanvändning i förhållande till effektiva gränser")

## <a name="queries-per-second-qps-and-other-metrics"></a>Frågor per sekund (QPS) och andra mått

Den **övervakning** fliken visar flytta medelvärden för mått som Sök *frågor Per sekund* (QPS), aggregerade per minut. 
*Svarstid för sökningar* avser mängden tid som behövs för att bearbeta sökfrågor, aggregerade per minut i söktjänsten. *Begränsade frågar procent* (visas inte) är procentandelen av sökfrågor som har begränsats också samman per minut.

Dessa siffror är ungefärliga och är avsedda att ge dig en allmän uppfattning av hur väl dina system betjänar förfrågningar. Faktiska Indexlagring kan vara högre eller lägre än det antal som rapporterats i portalen.

![Frågor per sekund aktivitet](./media/search-monitor-usage/monitoring-tab.png "frågor per sekund aktivitet")

## <a name="activity-logs"></a>Aktivitetsloggar

Den **aktivitetsloggen** samlar in information från Azure Resource Manager. Exempel på information som finns i aktivitetsloggen är skapa eller ta bort en tjänst, uppdaterar en resursgrupp, kontroll av namntillgänglighet eller lägga till en åtkomstnyckel för att hantera en begäran. 

Du kan komma åt den **aktivitetsloggen** från det vänstra navigeringsfönstret eller från meddelanden i det övre fönstret kommando fältet eller från den **diagnostisera och lösa problem** sidan.

För av uppgifter som att skapa ett index eller ta bort en datakälla visas allmänna meddelanden som ”hämta Admin Key” för varje begäran, men inte åtgärden själva. Du måste aktivera en lösning för övervakning av tillägg för den här nivån av information.

## <a name="add-on-monitoring-solutions"></a>Tillägg övervakningslösningar

Azure Search lagrar inte några data utöver de objekt som den hanterar, vilket innebär att data har lagras externt log. Du kan konfigurera någon av resurserna nedan om du vill bevara loggdata. 

I följande tabell jämförs alternativen för att lagra loggar och lägger till djupgående övervakning av tjänståtgärder och frågearbetsbelastningar via Application Insights.

| Resurs | Används för |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Loggade händelser och mätvärden för fråga, baserat på scheman som nedan, ihop med användarhändelser i din app. Det här är den enda lösningen som tar användaråtgärder eller signaler i beräkningen, mappning av händelser från användarinitierad sökning, till skillnad från filtrera begäranden som skickas av programkoden. Om du vill använda den här metoden, kopiera och klistra in instrumentation kod till källfilerna vägen begära information till Application Insights. Mer information finns i [Söktrafikanalys](search-traffic-analytics.md). |
| [Azure Monitor-loggar](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Loggade händelser och mätvärden för fråga, baserat på scheman som nedan. Händelser loggas en Log Analytics-arbetsyta. Du kan köra frågor mot en arbetsyta som returnerar detaljerad information från loggen. Mer information finns i [Kom igång med Azure Monitor-loggar](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Loggade händelser och mätvärden för fråga, baserat på scheman som nedan. Händelser loggas på en blobbehållare och lagras i JSON-filer. Använda en JSON-redigerare för att visa innehållet i filen.|
| [Händelsehubb](https://docs.microsoft.com/azure/event-hubs/) | Loggade händelser och mått i frågan, baserat på de scheman som beskrivs i den här artikeln. Välj det som ett alternativt samling tjänst för mycket stora loggar. |

Både Azure Monitor-loggar och Blob storage är tillgängliga som en kostnadsfri, delad tjänst så att du kan prova utan kostnad för livslängden för dina Azure-prenumeration. Application Insights är kostnadsfritt att registrera och använda så länge application data ligger under vissa gränser (se den [sidan med priser](https://azure.microsoft.com/pricing/details/monitor/) information).

Nästa avsnitt vägleder dig genom stegen för att aktivera och använda Azure Blob storage för att samla in och komma åt loggdata som skapats av Azure Search-åtgärder.

## <a name="enable-logging"></a>Aktivera loggning

Loggning för indexerings- och arbetsbelastningar är inaktiverat som standard och beror på tilläggslösningar för både loggning infrastruktur och externa långtidslagring. Är de enda beständiga data i Azure Search de objekt som den skapar och hanterar, så loggar måste lagras på annan plats.

I det här avsnittet lär du dig att använda Blob storage kan lagra loggade händelser och mått.

1. [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) om du inte redan har ett. Du kan placera det i samma resursgrupp som Azure Search för att förenkla Rensa senare om du vill ta bort alla resurser som används i den här övningen.

2. Öppna din översiktssidan för söktjänsten. I det vänstra navigeringsfönstret, rulla ned till **övervakning** och klicka på **aktivera övervakning**.

   ![Aktivera övervakning](./media/search-monitor-usage/enable-monitoring.png "aktivera övervakning")

3. Välj de data som du vill exportera: Loggar, mått eller båda. Du kan kopiera den till ett lagringskonto, skickar den till en event hub eller exportera den till Azure Monitor-loggar.

   För arkivering till Blob storage, endast lagringskontot måste finnas. Behållare och blobbar skapas när loggdata exporteras.

   ![Konfigurera blob storage Arkiv](./media/search-monitor-usage/configure-blob-storage-archive.png "konfigurera blob storage-Arkiv")

4. Spara profilen.

5. Testa loggning genom att skapa eller ta bort objekt (skapar logghändelser) och genom att skicka frågor (genererar mått). 

Loggning är aktiverad när du sparar profilen. Behållare skapas endast när det finns en aktivitet till loggen eller mått. När data kopieras till ett lagringskonto, data formaterade som JSON och placeras i två behållare:

* Insights-logs-operationlogs: för search trafikloggar
* Insights-mått-pt1m: för mått

**Det tar en timme innan behållarna som visas i Blob storage. Det finns en blob, per timme per behållare.**

Du kan använda [Visual Studio Code](#download-and-open-in-visual-studio-code) eller en annan JSON-redigerare för att visa filerna. 

### <a name="example-path"></a>Exempel på sökväg

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Log-schema
BLOB-objekt som innehåller dina trafikloggar för search-tjänsten är strukturerade som beskrivs i det här avsnittet. Varje blob har en rotobjektet kallas **poster** som innehåller en matris med objekt i loggen. Varje blob innehåller poster för alla åtgärder som ägde rum under en och samma timme.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Tidsstämpel för åtgärden |
| resourceId |sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS-STANDARD-PROVIDERS /<br/> MICROSOFT. SÖK/SEARCHSERVICES/SEARCHSERVICE ” |Din resurs-ID |
| operationName |sträng |”Query.Search” |Åtgärdens namn |
| operationVersion |string |"2017-11-11" |Api-versionen som används |
| category |sträng |”OperationLogs” |konstant |
| resultType |sträng |”Lyckades” |Möjliga värden: Lyckad eller misslyckad |
| resultSignature |int |200 |Resultatkod för HTTP |
| . durationMS |int |50 |Varaktighet i millisekunder |
| properties |objekt |Se följande tabell |Objekt som innehåller åtgärden-specifika data |

**Egenskaper för schema**

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| Beskrivning |sträng |”Hämta /indexes('content')/docs” |Åtgärdens slutpunkt |
| Söka i data |string |"?search=AzureSearch&$count=true&api-version=2017-11-11" |Frågeparametrar |
| Dokument |int |42 |Antal bearbetade dokument |
| Indexnamn |sträng |”testindex” |Namnet på det index som är associerade med åtgärden |

## <a name="metrics-schema"></a>Mått-schema

Mått som avbildas för frågebegäranden.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| resourceId |sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS-STANDARD-PROVIDERS /<br/>MICROSOFT. SÖK/SEARCHSERVICES/SEARCHSERVICE ” |resurs-id |
| MetricName |sträng |”Svarstiden” |namnet på måttet |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |åtgärdens tidsstämpel |
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

## <a name="download-and-open-in-visual-studio-code"></a>Ladda ned och öppna i Visual Studio Code

Du kan använda valfri JSON-redigerare för att visa loggfilen. Om du inte har ett, rekommenderar vi [Visual Studio Code](https://code.visualstudio.com/download).

1. Öppna ditt Storage-konto i Azure-portalen. 

2. I det vänstra navigeringsfönstret, klickar du på **Blobar**. Du bör se **insights-logs-operationlogs** och **insights-mått-pt1m**. De här behållarna har skapats av Azure Search när loggdata exporteras till Blob storage.

3. Klicka på ned mapphierarkin tills du når JSON-fil.  Använd på snabbmenyn för att hämta filen.

När filen har hämtats, kan du öppna den i en JSON-redigerare för att visa innehållet.

## <a name="use-system-apis"></a>Använd system-API
Både Azure Search REST API och .NET-SDK: N ger programmatisk åtkomst till information om tjänstens mått, index och indexerare och dokumentantal.

* [Hämta statistik för tjänster](/rest/api/searchservice/get-service-statistics)
* [Hämta Indexstatistiken](/rest/api/searchservice/get-index-statistics)
* [Antal dokument](/rest/api/searchservice/count-documents)
* [Hämta Status för indexerare](/rest/api/searchservice/get-indexer-status)

Om du vill aktivera med PowerShell eller Azure CLI finns i dokumentationen [här](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>Nästa steg

[Hantera din söktjänst på Microsoft Azure](search-manage.md) för mer information om tjänstadministration och [prestanda och optimering](search-performance-optimization.md) för justering vägledning.