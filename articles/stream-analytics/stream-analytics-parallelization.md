---
title: Använd frågeparallellisering och skala i Azure Stream Analytics
description: Den här artikeln beskriver hur du skalar Stream Analytics-jobb genom att konfigurera inkommande partitioner, justera frågedefinitionen och ställa in jobb enheter för strömning.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: d1afb6037b5fc290de93faba405982ebd1fb68ea
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364586"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Utnyttja frågeparallellisering i Azure Stream Analytics
Den här artikeln visar hur du drar nytta av parallellisering i Azure Stream Analytics. Du lär dig hur du skalar Stream Analytics-jobb genom att konfigurera inkommande partitioner och justera frågedefinitionen analytics.
Som ett krav kan du vilja vara bekant med begreppet enhet för strömning som beskrivs i [förstå och justera strömnings enheter](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Vilka är delarna av ett Stream Analytics-jobb?
Ett Stream Analytics-jobbdefinitionen innehåller indata, en fråge- och utdata. Indata är där jobbet läser från dataströmmen. Frågan används för att omvandla Indataströmmen data och utdata är där jobbet skickar resultatet till jobbet.

Ett jobb kräver minst en Indatakällan för strömmande data. Datakälla för stream inkommande kan lagras i en Azure-händelsehubb eller i Azure blob storage. Mer information finns i [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md) och [kom igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partitioner i källor och mottagare
Skala ett Stream Analytics-jobb drar nytta av partitioner i in- eller utdata. Partitionering kan du dela upp data i delmängder baserat på en partitionsnyckel. En process som använder data (till exempel ett Streaming Analytics-jobb) kan använda och skriva olika partitioner parallellt, vilket ökar dataflödet. 

### <a name="inputs"></a>Indata
Indata för alla Azure Stream Analytics kan dra nytta av partitionering:
-   EventHub (du behöver ange partitionsnyckel explicit med PARTITION BY nyckelord)
-   IoT Hub (du behöver ange partitionsnyckel explicit med PARTITION BY nyckelord)
-   Blob Storage

### <a name="outputs"></a>Utdata

När du arbetar med Stream Analytics kan dra du nytta av partitionering i utdata:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure-tabell
-   BLOB-lagring (kan ange Partitionsnyckeln uttryckligen)
-   Cosmos DB (du måste uttryckligen ange partitionsnyckel)
-   Event Hubs (du måste uttryckligen ange partitionsnyckel)
-   IoT Hub (du behöver ange Partitionsnyckeln uttryckligen)
-   Service Bus
- SQL och SQL Data Warehouse med valfri partitionering: Mer information finns på [sidan utdata till Azure SQL Database](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Power BI stöder inte partitionering. Du kan dock fortfarande partitionera indatamängden enligt beskrivningen i [det här avsnittet](#multi-step-query-with-different-partition-by-values) 

Mer information om partitioner finns i följande artiklar:

* [Översikt över Event Hubs-funktioner](../event-hubs/event-hubs-features.md#partitions)
* [Datapartitionering](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Embarrassingly parallella jobb
Ett *köras-parallellt* jobb är det mest skalbara scenariot som vi har i Azure Stream Analytics. En partition av indata till en instans av frågan ansluter den till en partition av utdata. Den här parallellitet har följande krav:

1. Om din frågelogiken är beroende av samma nyckel som bearbetas av samma fråga instans, måste du se till att händelserna går till samma partition som dina indata. För Event Hubs eller IoT Hub innebär det att händelse data måste ha **PartitionKey** -värdet inställt. Du kan också använda partitionerade avsändare. För blob-lagring innebär detta att händelser skickas till samma partition mapp. Du kan ignorera det här kravet om frågans logik inte kräver samma nyckel som ska bearbetas av samma fråga-instans. Ett exempel på den här logiken är en enkel select-projekt-filter-fråga.  

2. När data är placerade på inkommande sida, måste du kontrollera att frågan är partitionerad. Detta kräver att du använder **partition** i alla steg. Flera steg tillåts, men de måste vara partitionerad med samma nyckel. Under kompatibilitetsnivå 1,0 och 1,1 måste partitionerings nyckeln anges till **PartitionID** för att jobbet ska vara helt parallellt. För jobb med compatility nivå 1,2 och högre kan anpassade kolumner anges som partitionsnyckel i indatamängden och jobbet kommer att paralellized automatiskt trots att det inte finns någon PARTITION BY-sats. För Event Hub-utdata måste egenskapen "partitionsnyckel" vara inställd på att använda "PartitionId".

3. De flesta av våra utdata kan dra nytta av partitionering, men om du använder en Utdatatyp som inte stöder partitionering jobbet inte fullständigt parallella. Mer information finns i [avsnittet utdata](#outputs) .

4. Antalet inkommande partitioner måste vara lika med antalet partitioner som utdata. BLOB storage-utdata kan hantera partitioner och ärver partitioneringsschemat för den överordnade frågan. När en partitionsnyckel för Blob storage har angetts data är partitionerad per indatapartitionen därför är resultatet fortfarande helt parallella. Här följer exempel på partitionen värden som gör att ett fullständigt parallella jobb:

   * inkommande 8 händelsenavspartitioner och 8 händelsehubb utdata partitioner
   * inkommande 8 händelsenavspartitioner och blob storage-utdata
   * 8 inkommande händelsenavspartitioner och blob storage-utdata partitioneras efter ett anpassat fält med godtyckliga kardinalitet
   * 8 blob storage inkommande partitioner och blob storage-utdata
   * 8 blob partitioner för lagring av indata och utdata 8 händelsenavspartitioner

I följande avsnitt beskrivs några exempelscenarier som är embarrassingly parallel.

### <a name="simple-query"></a>Exempelfråga

* Indata: Event hub med 8 partitioner
* Utdata: Event Hub med 8 partitioner ("partitionsnyckel" måste anges för att använda "PartitionId")

Fråga:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Den här frågan är ett enkelt filter. Därför behöver vi inte bekymra dig om att partitionera indata som skickas till händelsehubben. Observera att jobb med kompatibilitetsnivå före 1,2 måste innehålla **partition by PartitionID** -sats, så att den uppfyller kravet #2 från tidigare. För utdata måste vi konfigurera Event Hub-utdata i jobbet så att partitionsnyckel anges till **PartitionID**. En senaste kontrollen är att se till att antalet inkommande partitioner är lika med antalet partitioner som utdata.

### <a name="query-with-a-grouping-key"></a>Fråga med en grupperingsnyckel

* Indata: Event hub med 8 partitioner
* Utdata: Blob storage

Fråga:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Den här frågan har en gruppering. Därför måste de händelser som grupperas tillsammans skickas till samma partition i Händelsehubben. Eftersom vi i det här exemplet Gruppera efter TollBoothID, ska vi se till att TollBoothID används som partitionsnyckel när händelser skickas till Event Hub. I ASA kan vi använda **partition av PartitionID** för att ärva från det här partitionsnamnet och aktivera fullständig parallellisering. Eftersom utdatan är blob-lagring, behöver vi inte bekymra dig om hur du konfigurerar ett partitionsnyckelvärde, enligt krav #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exempel på scenarier som *inte* är köras parallella

I det föregående avsnittet visade vi några embarrassingly parallel scenarier. I det här avsnittet diskuterar vi scenarier som inte uppfyller alla krav för att vara embarrassingly parallel. 

### <a name="mismatched-partition-count"></a>Ett felmatchat partitions-antal
* Indata: Event hub med 8 partitioner
* Utdata: Händelsehubb med 32 partitioner

Det spelar i det här fallet frågan är. Om antalet inkommande partitioner inte matchar antalet partitioner utdata, inte topologin embarrassingly parallellt. + men fortfarande få vissa nivå eller parallellisering.

### <a name="query-using-non-partitioned-output"></a>Fråga med hjälp av icke-partitionerad utdata
* Indata: Event hub med 8 partitioner
* Utdata: Power BI

Power BI-utdata stöder för närvarande inte partitionering. Det här scenariot är därför inte embarrassingly parallel.

### <a name="multi-step-query-with-different-partition-by-values"></a>Flera steg fråga med olika PARTITION BY-värden
* Indata: Event hub med 8 partitioner
* Utdata: Event hub med 8 partitioner

Fråga:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Som du kan se använder det andra steget **TollBoothId** som partitionerings nyckel. Det här steget är inte detsamma som det första steget och därför måste vi ska utföra en shuffle. 

I föregående exempel visas några Stream Analytics-jobb som överensstämmer med (inte eller) en embarrassingly parallel topologi. Om de uppfyller har risken för maximal skala. Uppdaterar för jobb som inte passar in en av de här profilerna skalning vägledning kommer att vara tillgängliga i framtiden. Använd den allmänna riktlinjen i följande avsnitt.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Kompatibilitetsnivå 1,2-multi-Step-fråga med en annan PARTITION efter värden 
* Indata: Event hub med 8 partitioner
* Utdata: Event Hub med 8 partitioner ("partitionsnyckel" måste anges för att använda "TollBoothId")

Fråga:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Kompatibilitetsnivån 1,2 aktiverar parallell frågekörning som standard. Till exempel är fråga från föregående avsnitt parttioned så länge som "TollBoothId"-kolumnen har angetts som indatamask för inpartitioner. Det krävs inte någon PARTITION BY ParttionId-sats.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Beräkna max strömningsenheter för ett jobb
Det totala antalet enheter för strömning som kan användas av ett Stream Analytics-jobb beror på hur många av stegen i frågan som definierats för jobbet och antalet partitioner för varje steg.

### <a name="steps-in-a-query"></a>Stegen i en fråga
En fråga kan ha en eller flera steg. Varje steg är en under fråga som definierats av nyckelordet **with** . Frågan som ligger utanför **with** -nyckelordet (endast en fråga) räknas också som ett steg, till exempel **Select** -uttrycket i följande fråga:

Fråga:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

Den här frågan har två steg.

> [!NOTE]
> Den här frågan beskrivs mer utförligt senare i artikeln.
>  

### <a name="partition-a-step"></a>Partitionera ett steg
Partitionera ett steg kräver följande förutsättningar:

* Indatakällan partitioneras. 
* **Select** -uttrycket för frågan måste läsa från en partitionerad indatakälla.
* Frågan i steget måste ha **partitionen med** nyckelord.

När en fråga är partitionerad inkommande händelser bearbetade och sammanställda i separata partitionsgrupper och utdata-händelser genereras för var och en av grupperna. Om du vill att en kombinerad aggregering, måste du skapa ett andra icke-partitionerad steg ska aggregeras.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Beräkna max strömningsenheter för ett jobb
Alla icke-partitionerad steg kan tillsammans skala upp till sex strömningsenheter (su) för ett Stream Analytics-jobb. Utöver detta är kan du lägga till 6 su: er för varje partition i en partitionerad steg.
Du kan se några **exempel** i tabellen nedan.

| Fråga                                               | Max SUs för jobbet |
| --------------------------------------------------- | ------------------- |
| <ul><li>Frågan innehåller ett steg.</li><li>Steget är inte partitionerad.</li></ul> | 6 |
| <ul><li>Inkommande data i dataströmmen har partitionerats med 16.</li><li>Frågan innehåller ett steg.</li><li>Steget är partitionerad.</li></ul> | 96 (6 * 16 partitioner) |
| <ul><li>Frågan innehåller två steg.</li><li>Inget av stegen är partitionerad.</li></ul> | 6 |
| <ul><li>Inkommande data i dataströmmen har partitionerats med 3.</li><li>Frågan innehåller två steg. Det inkommande steget är partitionerad och det andra steget är inte.</li><li><strong>Select</strong> -instruktionen läser från partitionerade inmatade.</li></ul> | 24 (18 partitionerade anvisningar + 6 för icke-partitionerad steg |

### <a name="examples-of-scaling"></a>Exempel på skalning

Följande fråga beräknar antalet bilar inom en tre tvåminutersperiod går igenom en avgift station som har tre tollbooths. Den här frågan kan skalas upp till sex su: er.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Om du vill använda flera SUs för frågan, måste både den inkommande dataströmmen och frågan partitioneras. Eftersom data stream partitionen har angetts till 3, kan följande ändrade frågan skalas upp till 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

När en fråga är partitionerad inkommande händelser bearbetas och aggregeras i en separat partitionsgrupper. Utdata-händelser genereras även för grupper. Partitionering kan orsaka oväntade resultat när fältet **Gruppera efter** inte är partitionsnyckel i indata-dataströmmen. Fältet **TollBoothId** i föregående fråga är till exempel inte partitionsnyckel för **INPUT1**. Resultatet är att data från vaktkur nr 1 kan spridas i flera partitioner.

Var och en av **INPUT1** -partitionerna bearbetas separat genom att Stream Analytics. Därmed skapas flera poster med antalet bil för samma vaktkur i samma utlösare för rullande fönster. Om inkommande Partitionsnyckeln inte kan ändras, kan det här problemet åtgärdas genom att lägga till ett icke-partition steg aggregerade värden över partitioner, som i följande exempel:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Den här frågan kan skalas till 24 SUs.

> [!NOTE]
> Om du ansluter till två dataströmmar, se till att strömmar partitioneras efter partitionsnyckel för kolumnen som används för att skapa kopplingarna. Kontrollera också att du har samma antal partitioner i båda strömmar.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Uppnå högre data flöden i stor skala

Ett [köras parallellt](#embarrassingly-parallel-jobs) jobb är nödvändigt men inte tillräckligt för att hantera ett högre data flöde i stor skala. Varje lagrings system och dess motsvarande Stream Analytics-utdata har variationer i hur du uppnår bästa möjliga Skriv data flöde. Precis som med alla storskaliga scenarier finns det vissa utmaningar som kan lösas med hjälp av rätt konfigurationer. I det här avsnittet beskrivs konfigurationer för några vanliga utdata och innehåller exempel på hur man kan ta del av förbruknings frekvensen på 1 KB, 5 K och 10 000 händelser per sekund.

I följande observationer används ett Stream Analytics jobb med en tillstånds lös (direkt lagrings fråga), en grundläggande JavaScript-UDF som skriver till Event Hub, Azure SQL DB eller Cosmos DB.

#### <a name="event-hub"></a>Händelsehubb

|Inmatnings frekvens (händelser per sekund) | Enheter för strömning | Utgående resurser  |
|--------|---------|---------|
| 1 000     |    1    |  2 DATA FLÖDES ENHETER   |
| 5 K     |    6    |  6 DATA FLÖDES ENHETER   |
| 000    |    12   |  10 DATA FLÖDES ENHETER  |

[Event Hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) -lösningen skalas linjärt i termer av strömnings enheter (SU) och data flöde, vilket gör det till det mest effektiva och bästa sättet att analysera och strömma data från Stream Analytics. Jobb kan skalas upp till 192 SU, som ungefär översätts till att bearbeta upp till 200 MB/s, eller 19 000 000 000 000 händelser per dag.

#### <a name="azure-sql"></a>Azure SQL
|Inmatnings frekvens (händelser per sekund) | Enheter för strömning | Utgående resurser  |
|---------|------|-------|
|    1 000   |   3  |  S3   |
|    5 K   |   18 |  P4   |
|    000  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) har stöd för skrivning parallellt, som kallas Ärv partitionering, men är inte aktiverat som standard. Att aktivera ärva partitionering, tillsammans med en helt parallell fråga, är dock inte tillräckligt för att uppnå högre data flöden. SQL Write-genomflöde är beroende av SQL Azure databas konfiguration och tabell schema. I artikeln [SQL-utdata](./stream-analytics-sql-output-perf.md) finns mer information om de parametrar som kan maximera Skriv data flödet. Som anges i [Azure Stream Analytics utdata till Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artikel skalar den här lösningen inte linjärt som en helt parallell pipeline utöver 8 partitioner och kan behöva partitionera om innan SQL-utdata (se [i](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Premium SKU: er krävs för att hantera höga IO-priser tillsammans med kostnader för att logga säkerhets kopieringar på några minuter.

#### <a name="cosmos-db"></a>Cosmos DB
|Inmatnings frekvens (händelser per sekund) | Enheter för strömning | Utgående resurser  |
|-------|-------|---------|
|  1 000   |  3    | 20 000 RU  |
|  5 K   |  24   | 60K RU  |
|  000  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) utdata från Stream Analytics har uppdaterats för att använda inbyggd integrering under [kompatibilitetsnivå 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Kompatibilitetsnivån 1,2 möjliggör betydligt högre genomflöde och minskar RU-förbrukningen jämfört med 1,1, vilket är standard kompatibilitetsnivån för nya jobb. Lösningen använder CosmosDB-behållare partitionerade på/deviceId och resten av lösningen har kon figurer ATS identiskt.

Alla [strömningar i Azure-exempel](https://github.com/Azure-Samples/streaming-at-scale) använder en Event Hub som matas in genom belastnings simulerings test klienter som indata. Varje indata-händelse är ett 1 KB JSON-dokument, som översätter de konfigurerade inmatnings priserna till data flödes nivåerna (1 MB/s, 5 MB/s och 10 MB/s) enkelt. Händelser simulerar en IoT-enhet som skickar följande JSON-data (i ett förkortat format) för upp till 1 kB-enheter:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> Konfigurationerna kan ändras på grund av de olika komponenter som används i lösningen. Om du vill ha en mer exakt uppskattning kan du anpassa exemplen efter ditt scenario.

### <a name="identifying-bottlenecks"></a>Identifiera Flask halsar

Använd fönstret mått i ditt Azure Stream Analytics jobb för att identifiera Flask halsar i din pipeline. Granska **indata/utdata-händelser** för data flöde och ["fördröjning av vattenstämpel"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) eller **eftersläpande händelser** för att se om jobbet hålls i takt med indata. För Event Hub-mått söker du efter **begränsade begär Anden** och justerar tröskel enheterna enligt detta. För Cosmos DB Mät värden granskar du **Max förbrukade ru/s per nyckel intervall** under genomflödet för att se till att dina partitionerings nyckel intervall är enhetligt förbrukade. Övervaka **logg-i/o** och **CPU**för Azure SQL DB.

## <a name="get-help"></a>Få hjälp

Om du behöver ytterligare hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

