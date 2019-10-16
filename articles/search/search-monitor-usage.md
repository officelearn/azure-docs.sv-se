---
title: Övervaka resursanvändning och fråga mått för en search-tjänst – Azure Search
description: Aktivera loggning, Hämta aktivitets mått, resursanvändning och andra system data från en Azure Search-tjänst.
author: HeidiSteen
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: heidist
ms.openlocfilehash: fe8061f8e99742f9dc5c1181235c4203aaad82ca
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331215"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Övervaka resursförbrukning och fråga-aktivitet i Azure Search

På sidan Översikt i din Azure Search-tjänst kan du Visa system information om resursanvändning, fråga mått och hur mycket kvot som finns för att skapa fler index, indexerare och data källor. Du kan också använda portalen för att konfigurera Log Analytics eller en annan resurs som används för beständig data insamling. 

Att konfigurera loggar är användbart för självstudier och bevarande av drift historik. Internt finns loggar på Server delen under en kort tids period, tillräckligt för undersökning och analys om du filerar ett support ärende. Om du vill ha kontroll över och åtkomst till logg information, bör du konfigurera en av lösningarna som beskrivs i den här artikeln.

I den här artikeln lär du dig hur du övervakar dina övervaknings alternativ, hur du aktiverar loggning och logg lagring och hur du visar logg innehåll.

## <a name="metrics-at-a-glance"></a>Snabbt och enkelt mått

Avsnitt om **användning** och **övervakning** som är inbyggda på översikts sidan rapporterar om resursanvändningen och mått för frågekörning. Den här informationen blir tillgänglig så snart du börjar använda tjänsten, utan konfiguration krävs. Den här sidan uppdateras varje minut. Om du slutför beslut om [vilken nivå som ska användas för produktions arbets belastningar](search-sku-tier.md), eller om du vill [Justera antalet aktiva repliker och partitioner](search-capacity-planning.md), kan dessa mått hjälpa dig med dessa beslut genom att visa hur snabbt resurser förbrukas och hur väl den aktuella konfigurationen hanterar den befintliga belastningen.

På fliken **användning** visas resurs tillgänglighet i förhållande till aktuella [gränser](search-limits-quotas-capacity.md). Följande bild är avsedd för den kostnads fria tjänsten som är ett tak på 3 objekt av varje typ och 50 MB lagrings utrymme. En Basic-eller standard-tjänst har högre gränser, och om du ökar antalet partitioner, hamnar det maximala lagrings utrymmet proportionellt.

![Användnings status relativt mot gällande gränser](./media/search-monitor-usage/usage-tab.png
 "användnings status i förhållande till effektiva gränser")

## <a name="queries-per-second-qps-and-other-metrics"></a>Frågor per sekund (frågor per sekund) och andra mått

Fliken **övervakning** visar glidande medelvärden för mått som Sök *frågor per sekund* (frågor per sekund), sammanställt per minut. 
*Sök fördröjning* är den tid som Sök tjänsten behöver för att bearbeta Sök frågor, sammanställt per minut. *Begränsade Sök frågor i procent* (visas inte) är procent andelen Sök frågor som har begränsats, även sammanlagt per minut.

Dessa tal är ungefärliga och är avsedda att ge dig en allmän uppfattning om hur väl systemet hanterar begär Anden. Det faktiska frågor per sekund kan vara högre eller lägre än det antal som rapporteras i portalen.

![Frågor per sekund]aktivitet(./media/search-monitor-usage/monitoring-tab.png "frågor per sekund aktivitet")

## <a name="activity-logs"></a>Aktivitetsloggar

**Aktivitets loggen** samlar in information från Azure Resource Manager. Exempel på information som finns i aktivitets loggen är att skapa eller ta bort en tjänst, uppdatera en resurs grupp, kontrol lera namn tillgänglighet eller hämta en tjänst åtkomst nyckel för att hantera en begäran. 

Du kan komma åt **aktivitets loggen** i det vänstra navigerings fönstret eller från meddelanden i det övre fönstrets kommando fält eller på sidan **diagnosticera och lösa problem** .

För tjänst uppgifter som att skapa ett index eller ta bort en data källa visas allmänna meddelanden som "Hämta administratörs nyckel" för varje begäran, men inte själva åtgärden. För den här informations nivån måste du aktivera en lösning för övervakning av tillägg.

## <a name="add-on-monitoring-solutions"></a>Lösningar för övervakning av tillägg

Azure Search lagrar inga data utöver de objekt som hanteras, vilket innebär att loggdata lagras externt. Du kan konfigurera någon av resurserna nedan om du vill spara loggdata. 

I följande tabell jämförs alternativen för att lagra loggar och lägga till djupgående övervakning av tjänst åtgärder och fråga arbets belastningar via Application Insights.

| Resurs | Används för |
|----------|----------|
| [Azure Monitor-loggar](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Loggade händelser och fråga mått baserat på scheman nedan. Händelser loggas till en Log Analytics-arbetsyta. Du kan köra frågor mot en arbets yta för att returnera detaljerad information från loggen. Mer information finns i [Kom igång med Azure Monitor loggar](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Loggade händelser och fråga mått baserat på scheman nedan. Händelser loggas till en BLOB-behållare och lagras i JSON-filer. Använd en JSON-redigerare för att visa fil innehåll.|
| [Händelsehubb](https://docs.microsoft.com/azure/event-hubs/) | Loggade händelser och fråga om mått baserat på de scheman som dokumenteras i den här artikeln. Välj det här som en alternativ data insamlings tjänst för mycket stora loggar. |

Både Azure Monitor-loggar och blob-lagring är tillgängliga som en kostnads fri tjänst så att du kan testa den utan kostnad för Azure-prenumerationens livs längd. Application Insights är kostnads fri att registrera dig och använda så länge program data storleken är under vissa gränser (se [sidan med priser](https://azure.microsoft.com/pricing/details/monitor/) för mer information).

I nästa avsnitt får du stegvisa anvisningar om hur du aktiverar och använder Azure Blob Storage för att samla in och få åtkomst till loggdata som skapats av Azure Search åtgärder.

## <a name="enable-logging"></a>Aktivera loggning

Loggning av indexerings-och fråge arbets belastningar är inaktiverat som standard och är beroende av tilläggs lösningar för både loggning av infrastruktur och långsiktig extern lagring. De enda beständiga data i Azure Search är de objekt som skapas och hanteras, så loggar måste lagras någon annan stans.

I det här avsnittet får du lära dig hur du använder Blob Storage för att lagra loggade händelser och mått data.

1. [Skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) om du inte redan har ett. Du kan placera den i samma resurs grupp som Azure Search för att förenkla rensningen senare om du vill ta bort alla resurser som används i den här övningen.

   Ditt lagrings konto måste finnas i samma region som Azure Search.

2. Öppna översikts sidan för Sök tjänsten. Rulla ned till **övervakning** i det vänstra navigerings fönstret och klicka på **Aktivera övervakning**.

   ![Aktivera]övervakning(./media/search-monitor-usage/enable-monitoring.png "Aktivera övervakning")

3. Välj de data som du vill exportera: loggar, mått eller båda. Du kan kopiera det till ett lagrings konto, skicka det till en Event Hub eller exportera det till Azure Monitor loggar.

   Endast lagrings kontot måste finnas för arkivering till blob-lagring. Behållare och blobbar skapas som nödvändiga när loggdata exporteras.

   ![Konfigurera Arkiv för Blob Storage](./media/search-monitor-usage/configure-blob-storage-archive.png "Konfigurera Blob Storage-Arkiv")

4. Spara profilen.

5. Testa loggning genom att skapa eller ta bort objekt (skapar logg händelser) och genom att skicka frågor (genererar mått). 

Loggning aktive ras när du sparar profilen. Behållare skapas endast när det finns en aktivitet som ska loggas eller mätas. När data kopieras till ett lagrings konto formateras data som JSON och placeras i två behållare:

* Insights-logs-operationlogs: för Sök trafik loggar
* Insights-mått – pt1m: för mått

**Det tar en timme innan behållarna visas i Blob Storage. Det finns en BLOB, per timme, per behållare.**

Du kan använda [Visual Studio Code](#download-and-open-in-visual-studio-code) eller en annan JSON-redigerare för att visa filerna. 

### <a name="example-path"></a>Exempel Sök väg

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Logg schema
Blobbar som innehåller dina Sök tjänst trafik loggar struktureras enligt beskrivningen i det här avsnittet. Varje Blob har ett rot objekt som kallas **poster** som innehåller en matris med logg objekt. Varje Blob innehåller poster för alla åtgärder som ägde rum under samma timme.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |Tidsstämpel för åtgärden |
| resourceId |sträng |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDARD/PROVIDERS/<br/> Utforskaren. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Din ResourceId |
| operationName |sträng |"Fråga. search" |Åtgärdens namn |
| operationVersion |sträng |"2019-05-06" |Den API-version som används |
| category |sträng |"OperationLogs" |konstant |
| resultType |sträng |Resultatet |Möjliga värden: lyckades eller misslyckades |
| resultSignature |int |200 |HTTP-resultat kod |
| . Durationms |int |50 |Åtgärdens varaktighet i millisekunder |
| properties |objekt |Se följande tabell |Objekt som innehåller åtgärds information |

**Egenskaps schema**

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| Beskrivning |sträng |"Hämta/Indexes (' Content ')/docs" |Åtgärdens slut punkt |
| Söka i data |sträng |"? search = AzureSearch & $count = True & API-version = 2019-05-06" |Frågeparametrar |
| Dokument |int |42 |Antal bearbetade dokument |
| indexName |sträng |"testindex" |Namnet på det index som är associerat med åtgärden |

## <a name="metrics-schema"></a>Mått schema

Måtten fångas för fråge förfrågningar.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| resourceId |sträng |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDARD/PROVIDERS/<br/>Utforskaren. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |resurs-ID |
| metricName |sträng |Svars tid |namnet på måttet |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |åtgärdens tidsstämpel |
| snitt |int |64 |Genomsnittligt värde för RAW-exemplen i Metric-tidsintervallet |
| Lägst |int |37 |Det minimala värdet för RAW-exemplen i måttets tidsintervall |
| Maximihalter |int |78 |Det maximala värdet för RAW-exemplen i måttets tidsintervall |
| Totalt |int |258 |Det totala värdet för RAW-exemplen i måttets tidsintervall |
| count |int |4 |Antalet RAW-exempel som används för att generera måttet |
| timegrain |sträng |"PT1M" |Tids kornig het för måttet i ISO 8601 |

Alla mått rapporteras i intervall på en minut. Varje mått visar de lägsta, högsta och genomsnittliga värdena per minut.

För SearchQueriesPerSecond-måttet är minimivärdet det lägsta värdet för Sök frågor per sekund som registrerades under den minuten. Samma gäller för det högsta värdet. Average, är aggregatet över hela minuten.
Tänk på det här scenariot under en minut: en sekund med hög belastning som är max för SearchQueriesPerSecond, följt av 58 sekunders genomsnittlig belastning och slutligen en sekund med bara en fråga, vilket är minimivärdet.

För ThrottledSearchQueriesPercentage, lägsta, högsta, medelvärde och total, har alla samma värde: procent andelen Sök frågor som har begränsats från det totala antalet Sök frågor under en minut.

## <a name="download-and-open-in-visual-studio-code"></a>Ladda ned och öppna i Visual Studio Code

Du kan Visa logg filen med valfri JSON-redigerare. Om du inte har en sådan rekommenderar vi [Visual Studio Code](https://code.visualstudio.com/download).

1. I Azure Portal öppnar du ditt lagrings konto. 

2. Klicka på **blobbar**i det vänstra navigerings fönstret. Du bör se **Insights-logs-operationlogs** och **Insights-Metrics-pt1m**. Dessa behållare skapas av Azure Search när loggdata exporteras till Blob Storage.

3. Klicka på mapphierarkin tills du når. JSON-filen.  Använd snabb menyn för att hämta filen.

När filen har hämtats öppnar du den i en JSON-redigerare för att visa innehållet.

## <a name="use-system-apis"></a>Använda system-API: er
Både Azure Search REST API och .NET SDK ger programmerings åtkomst till tjänst statistik, index-och index information och dokument antal.

* [Hämta tjänste statistik](/rest/api/searchservice/get-service-statistics)
* [Hämta index statistik](/rest/api/searchservice/get-index-statistics)
* [Räkna dokument](/rest/api/searchservice/count-documents)
* [Hämta indexerings status](/rest/api/searchservice/get-indexer-status)

Information om hur du aktiverar med PowerShell eller Azure CLI finns i dokumentationen [här](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Nästa steg

[Hantera din Sök tjänst på Microsoft Azure](search-manage.md) för mer information om tjänst administration och [prestanda och optimering](search-performance-optimization.md) för justerings vägledning.
