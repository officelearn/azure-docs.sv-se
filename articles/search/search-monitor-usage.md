---
title: Övervaka användning och fråga Resursstatistik för en search-tjänst – Azure Search
description: Hämta fråga aktivitetsmått, resursförbrukning och andra systemdata från en Azure Search-tjänst.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5f8a4e7dcaa1bc2df71246f67d06fc63ae4fcd06
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883508"
---
# <a name="monitor-an-azure-search-service-in-azure-portal"></a>Övervaka en Azure Search-tjänst i Azure-portalen

Du kan visa systemdata om resursanvändning, plus fråga mått som frågor Per sekund (QPS), frågesvarstiden och procentandelen förfrågningar som har begränsats på sidan Översikt i Azure Search-tjänsten. Du kan dessutom använda portalen för att utnyttja en mängd funktioner för övervakning i Azure-plattformen för djupare datainsamling. 

Den här artikeln identifierar och jämför tillgängliga alternativ för loggning av Azure Search-åtgärder. Guiden innehåller anvisningar för att aktivera loggning och logglagring och hur du expanderar på den information som samlas in.

Om du arkiverar ett supportärende, finns det inga specifika uppgifter eller information som du måste ange. Support-tekniker har nödvändig information för att undersöka specifika problem.  

## <a name="metrics-at-a-glance"></a>Mått direkt

**Användning** och **övervakning** avsnitt som är inbyggda i översikt över visualisera lagringsanvändningen och frågar om mått för körning. Den här informationen blir tillgänglig när du börjar använda tjänsten, med krävs ingen konfiguration. Den här sidan uppdateras några minuters mellanrum. Om du slutför beslut om [vilken nivå ska användas för produktionsarbetsbelastningar](search-sku-tier.md), eller om du vill [justera antalet aktiva repliker och partitioner](search-capacity-planning.md), de här måtten kan hjälpa dig med dessa beslut där du kan se hur snabbt resurser används och hur väl den aktuella konfigurationen hanterar befintlig belastning.

Den **användning** fliken visar resurstillgänglighet i förhållande till aktuell [gränser](search-limits-quotas-capacity.md). Följande bild är för den kostnadsfria tjänsten som är högst 3 objekt av varje typ och 50 MB lagringsutrymme. Basic eller Standard-tjänsten har högre gränser, och om du ökar antalet för partition, det största lagringsutrymmet går upp proportionellt.

![Status för användning i förhållande till effektiva gränser](./media/search-monitor-usage/usage-tab.png
 "för programanvändning i förhållande till effektiva gränser")

## <a name="queries-per-second-qps-and-other-metrics"></a>Frågor per sekund (QPS) och andra mått

Den **övervakning** fliken visar flytta medelvärden för mått som Sök *frågor Per sekund* (QPS), aggregerade per minut. 
*Svarstid för sökningar* avser mängden tid som behövs för att bearbeta sökfrågor, aggregerade per minut i söktjänsten. *Begränsade frågar procent* (visas inte) är procentandelen av sökfrågor som har begränsats också samman per minut.

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
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | [Söktrafikanalys](search-traffic-analytics.md). Det här är den enda lösningen som samlar in ytterligare information om förfrågningar, bortom de värden som anges i scheman för loggning och mått som nedan. Med den här metoden kan du kopiera och klistra in koden för instrumentation i källfilerna för att dirigera begäraninformation till Application Insights för analys på frågan termen indata och frågor med noll matchningar, och så vidare. Power BI som analytics klientdel rekommenderar vi att data som lagras i Application Insights.  |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Förfrågningar och mått, baserade på något scheman nedan. Händelser loggas på en blobbehållare. Vi rekommenderar Excel eller Power BI som analytics klientdelen till lagrade data i Azure Blob storage.|
| [Händelsehubb](https://docs.microsoft.com/azure/event-hubs/) | Begäranden och mått, baserat på de scheman som beskrivs i den här artikeln. Välj det som ett alternativt samling tjänst för mycket stora loggar. |

Azure search har ett övervakning [Power BI-Innehållspaketet](https://app.powerbi.com/getdata/services/azure-search) så att du kan analysera loggdata. Innehållspaketet består av rapporter som är konfigurerad för att automatiskt ansluta till dina data och ger visuella insikter om din söktjänst. Mer information finns i den [hjälpsidan för Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

Alternativ för Blob-lagring är tillgänglig som kostnadsfri delad tjänst så att du kan prova utan kostnad för livslängden för dina Azure-prenumeration. Nästa avsnitt vägleder dig genom stegen för att aktivera och använda Azure Blob storage för att samla in och komma åt loggdata som skapats av Azure Search-åtgärder.

## <a name="enable-logging"></a>Aktivera loggning

Loggning för indexerings- och arbetsbelastningar är inaktiverat som standard och beror på tilläggslösningar för både loggning infrastruktur och extern lagring. Är de enda beständiga data i Azure Search index, så loggar måste lagras på annan plats.

I det här avsnittet lär du dig att använda Blob storage innehåller loggade händelser och mått data.

1. [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) om du inte redan har ett. Du kan placera det i samma resursgrupp som Azure Search för att förenkla Rensa senare om du vill ta bort alla resurser som används i den här övningen.

2. Öppna din översiktssidan för söktjänsten. I det vänstra navigeringsfönstret, rulla ned till **övervakning** och klicka på **aktivera övervakning**.

   ![Aktivera övervakning](./media/search-monitor-usage/enable-monitoring.png "aktivera övervakning")

3. Välj de data som du vill exportera: Loggar, mått eller båda. Du kan kopiera den till ett lagringskonto, skickar den till en event hub eller exportera den till Log Analytics.

   För arkivering till Blob storage, endast lagringskontot måste finnas. Behållare och blobbar skapas när loggdata exporteras.

   ![Konfigurera blob storage Arkiv](./media/search-monitor-usage/configure-blob-storage-archive.png "konfigurera blob storage-Arkiv")

4. Spara profilen.

5. Testa loggning genom att skapa eller ta bort objekt (genererar en arbetsloggen) och genom att skicka frågor (genererar mått). 

Loggning är aktiverat när du sparar profilen, behållare skapas endast när det finns en händelse till loggen eller mått. Det kan ta flera minuter innan behållarna ska visas. Du kan [visualisera data i Power BI](#analyze-with-power-bi) när den blir tillgänglig.

När data kopieras till ett lagringskonto, data formaterade som JSON och placeras i två behållare:

* Insights-logs-operationlogs: för search trafikloggar
* Insights-mått-pt1m: för mått

Det finns en blob, per timme per behållare.

Exempel på sökväg: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json`

## <a name="log-schema"></a>Log-schema
BLOB-objekt som innehåller dina trafikloggar för search-tjänsten är strukturerade som beskrivs i det här avsnittet. Varje blob har en rotobjektet kallas **poster** som innehåller en matris med objekt i loggen. Varje blob innehåller poster för alla åtgärder som ägde rum under en och samma timme.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Tidsstämpel för åtgärden |
| resourceId |sträng |”/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS-STANDARD-PROVIDERS /<br/> MICROSOFT. SÖK/SEARCHSERVICES/SEARCHSERVICE ” |Din resurs-ID |
| operationName |sträng |”Query.Search” |Åtgärdens namn |
| operationVersion |sträng |"2017-11-11" |Api-versionen som används |
| category |sträng |”OperationLogs” |konstant |
| resultType |sträng |”Lyckades” |Möjliga värden: Lyckad eller misslyckad |
| resultSignature |int |200 |Resultatkod för HTTP |
| . durationMS |int |50 |Varaktighet i millisekunder |
| properties |objekt |Se följande tabell |Objekt som innehåller åtgärden-specifika data |

**Egenskaper för schema**

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| Beskrivning |sträng |”Hämta /indexes('content')/docs” |Åtgärdens slutpunkt |
| Söka i data |sträng |"?search=AzureSearch&$count=true&api-version=2017-11-11" |Frågeparametrar |
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

## <a name="analyze-with-power-bi"></a>Analysera med Powerbi

Vi rekommenderar att du använder [Power BI](https://powerbi.microsoft.com) att utforska och visualisera dina data, särskilt om du har aktiverat [söktrafikanalys](search-traffic-analytics.md). Mer information finns i den [hjälpsidan för Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

Anslutningar kräver lagringskontots namn och åtkomstnyckel, som du kan hämta från Azure portal sidor på den **åtkomstnycklar** sida av instrumentpanelen storage-konto.

1. Installera den [Power BI-innehållspaket](https://app.powerbi.com/getdata/services/azure-search). Innehållspaketet lägger till fördefinierade diagram och tabeller som är användbar för att analysera den ytterligare data som hämtats för söktrafikanalys. 

   Om du använder Blob-lagring eller en annan mekanism för lagring och du har inte angett instrumentation i koden, kan du hoppa över Innehållspaketet och använder inbyggda Power BI-visualiseringar.

2. Öppna **Power BI**, klickar du på **hämta Data** > **Services** > **Azure Search**.

3. Ange namnet på lagringskontot, Välj **nyckel** för autentisering och sedan klistra in en åtkomstnyckel.

4. Importera data och klicka sedan på **visa data**.

Följande skärmbild visar de inbyggda rapporterna och diagram för att analysera Sök trafikanalys.

![Power BI-instrumentpanel för Azure Search](./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png "Power BI-instrumentpanel för Azure Search")

## <a name="get-sys-info-apis"></a>Hämta sys-info API: er
Både Azure Search REST API och .NET-SDK: N ger programmatisk åtkomst till information om tjänstens mått, index och indexerare och dokumentantal.

* [Hämta statistik för tjänster](/rest/api/searchservice/get-service-statistics)
* [Hämta Indexstatistiken](/rest/api/searchservice/get-index-statistics)
* [Antal dokument](/rest/api/searchservice/count-documents)
* [Hämta Status för indexerare](/rest/api/searchservice/get-indexer-status)

Om du vill aktivera med PowerShell eller Azure CLI finns i dokumentationen [här](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>Nästa steg

[Hantera din söktjänst på Microsoft Azure](search-manage.md) för mer information om tjänstadministration och [prestanda och optimering](search-performance-optimization.md) för justering vägledning.

Lär dig mer om att skapa fantastiska rapporter. Se [komma igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) mer information.

