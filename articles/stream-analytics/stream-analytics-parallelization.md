---
title: Använd frågeparallellisering och skala i Azure Stream Analytics
description: Den här artikeln beskriver hur du skalar Stream Analytics-jobb genom att konfigurera inkommande partitioner, justera frågedefinitionen och ställa in jobb enheter för strömning.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 4fd862c2442d2637d799a1f690d5f0a091c80562
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449199"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Utnyttja frågeparallellisering i Azure Stream Analytics
Den här artikeln visar hur du drar nytta av parallellisering i Azure Stream Analytics. Du lär dig hur du skalar Stream Analytics-jobb genom att konfigurera inkommande partitioner och justera frågedefinitionen analytics.
Som ett krav kan du vara bekant med begreppet enhet för strömning som beskrivs i [förstå och justera Direktuppspelningsenheter](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Vilka är delarna av ett Stream Analytics-jobb?
Ett Stream Analytics-jobbdefinitionen innehåller indata, en fråge- och utdata. Indata är där jobbet läser från dataströmmen. Frågan används för att omvandla Indataströmmen data och utdata är där jobbet skickar resultatet till jobbet.

Ett jobb kräver minst en Indatakällan för strömmande data. Datakälla för stream inkommande kan lagras i en Azure-händelsehubb eller i Azure blob storage. Mer information finns i [introduktion till Azure Stream Analytics](stream-analytics-introduction.md) och [komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

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
-   Cosmos DB (du behöver ange Partitionsnyckeln uttryckligen)
-   Händelsehubbar (du behöver ange Partitionsnyckeln uttryckligen)
-   IoT Hub (du behöver ange Partitionsnyckeln uttryckligen)
-   Service Bus
- SQL- och SQL Data Warehouse med valfritt partitionering: se mer information om den [utdata till Azure SQL Database-sidan](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Powerbi stöder inte partitionering. Men du kan fortfarande partitionera indata enligt beskrivningen i [i det här avsnittet](#multi-step-query-with-different-partition-by-values) 

Mer information om partitioner finns i följande artiklar:

* [Översikt över Event Hubs-funktioner](../event-hubs/event-hubs-features.md#partitions)
* [Datapartitionering](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Embarrassingly parallella jobb
En *embarrassingly parallel* jobbet är det mest skalbara scenariot som vi har i Azure Stream Analytics. En partition av indata till en instans av frågan ansluter den till en partition av utdata. Den här parallellitet har följande krav:

1. Om din frågelogiken är beroende av samma nyckel som bearbetas av samma fråga instans, måste du se till att händelserna går till samma partition som dina indata. Det innebär att informationen om händelsen måste ha för Event Hubs eller IoT Hub, den **PartitionKey** set-värde. Du kan också använda partitionerade avsändare. För blob-lagring innebär detta att händelser skickas till samma partition mapp. Du kan ignorera det här kravet om frågans logik inte kräver samma nyckel som ska bearbetas av samma fråga-instans. Ett exempel på den här logiken är en enkel select-projekt-filter-fråga.  

2. När data är placerade på inkommande sida, måste du kontrollera att frågan är partitionerad. Detta måste du använda **PARTITION BY** i alla steg. Flera steg tillåts, men de måste vara partitionerad med samma nyckel. Under kompatibilitetsnivå 1.0 och 1.1, partitionsnyckel som måste vara inställt på **PartitionId** i ordning tills jobbet helt parallell. Anpassad kolumn kan anges som partitionsnyckel i inställningarna för indata och jobbet kommer att paralellized automoatically även om du inte PARTITION BY-satsen för jobb med compatility nivå 1.2 och högre.

3. De flesta av våra utdata kan dra nytta av partitionering, men om du använder en Utdatatyp som inte stöder partitionering jobbet inte fullständigt parallella. Referera till den [utdata avsnittet](#outputs) för mer information.

4. Antalet inkommande partitioner måste vara lika med antalet partitioner som utdata. BLOB storage-utdata kan hantera partitioner och ärver partitioneringsschemat för den överordnade frågan. När en partitionsnyckel för Blob storage har angetts data är partitionerad per indatapartitionen därför är resultatet fortfarande helt parallella. Här följer exempel på partitionen värden som gör att ett fullständigt parallella jobb:

   * inkommande 8 händelsenavspartitioner och 8 händelsehubb utdata partitioner
   * inkommande 8 händelsenavspartitioner och blob storage-utdata
   * 8 inkommande händelsenavspartitioner och blob storage-utdata partitioneras efter ett anpassat fält med godtyckliga kardinalitet
   * 8 blob storage inkommande partitioner och blob storage-utdata
   * 8 blob partitioner för lagring av indata och utdata 8 händelsenavspartitioner

I följande avsnitt beskrivs några exempelscenarier som är embarrassingly parallel.

### <a name="simple-query"></a>Exempelfråga

* Indata: Event hub med 8 partitioner
* Utdata: Event hub med 8 partitioner

Fråga:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Den här frågan är ett enkelt filter. Därför behöver vi inte bekymra dig om att partitionera indata som skickas till händelsehubben. Observera att jobb med kompatibilitetsnivå innan 1.2 måste innehålla **PARTITION av PartitionId** -satsen, så att den uppfyller krav #2 från tidigare. För utdata, vi måste konfigurera event hub-utdata i jobbet har partition nyckeluppsättning till **PartitionId**. En senaste kontrollen är att se till att antalet inkommande partitioner är lika med antalet partitioner som utdata.

### <a name="query-with-a-grouping-key"></a>Fråga med en grupperingsnyckel

* Indata: Event hub med 8 partitioner
* Utdata: Blob Storage

Fråga:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Den här frågan har en gruppering. Därför måste de händelser som grupperas tillsammans skickas till samma partition i Händelsehubben. Eftersom vi i det här exemplet Gruppera efter TollBoothID, ska vi se till att TollBoothID används som partitionsnyckel när händelser skickas till Event Hub. Sedan i ASA, använder vi **PARTITION av PartitionId** att ärva från den här partitionsschema och aktivera fullständig parallellisering. Eftersom utdatan är blob-lagring, behöver vi inte bekymra dig om hur du konfigurerar ett partitionsnyckelvärde, enligt krav #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exempel på scenarier som är *inte* embarrassingly parallel

I det föregående avsnittet visade vi några embarrassingly parallel scenarier. I det här avsnittet diskuterar vi scenarier som inte uppfyller alla krav för att vara embarrassingly parallel. 

### <a name="mismatched-partition-count"></a>Ett felmatchat partitions-antal
* Indata: Event hub med 8 partitioner
* Utdata: Händelsehubb med 32 partitioner

Det spelar i det här fallet frågan är. Om antalet inkommande partitioner inte matchar antalet partitioner utdata, inte topologin embarrassingly parallellt. + men fortfarande få vissa nivå eller parallellisering.

### <a name="query-using-non-partitioned-output"></a>Fråga med hjälp av icke-partitionerad utdata
* Indata: Event hub med 8 partitioner
* Utdata: Power BI

Power BI-utdata stöd inte för närvarande för partitionering. Det här scenariot är därför inte embarrassingly parallel.

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

Som du kan se det andra steget använder **TollBoothId** som partitionsnyckel. Det här steget är inte detsamma som det första steget och därför måste vi ska utföra en shuffle. 

I föregående exempel visas några Stream Analytics-jobb som överensstämmer med (inte eller) en embarrassingly parallel topologi. Om de uppfyller har risken för maximal skala. Uppdaterar för jobb som inte passar in en av de här profilerna skalning vägledning kommer att vara tillgängliga i framtiden. Använd den allmänna riktlinjen i följande avsnitt.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Kompatibilitetsnivån 1.2 - flerstegstest fråga med olika PARTITION BY-värden 
* Indata: Event hub med 8 partitioner
* Utdata: Event hub med 8 partitioner

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

Kompatibilitetsnivån 1.2 aktiverar parallell frågekörning som standard. Frågan från föregående avsnitt kommer exempelvis att parttioned så länge ”TollBoothId” kolumn anges som indata partitionsnyckel. PARTITIONEN av ParttionId-satsen är inte obligatoriskt.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Beräkna max strömningsenheter för ett jobb
Det totala antalet enheter för strömning som kan användas av ett Stream Analytics-jobb beror på hur många av stegen i frågan som definierats för jobbet och antalet partitioner för varje steg.

### <a name="steps-in-a-query"></a>Stegen i en fråga
En fråga kan ha en eller flera steg. Varje steg finns en underfråga som definieras av den **WITH** nyckelord. Den fråga som ligger utanför den **WITH** nyckelord (endast en fråga) också räknas som ett steg som den **Välj** instruktionen i följande fråga:

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
* Den **Välj** -uttrycket för frågan måste läsa från en partitionerad Indatakällan.
* Frågan i steget som måste ha den **PARTITION BY** nyckelord.

När en fråga är partitionerad inkommande händelser bearbetade och sammanställda i separata partitionsgrupper och utdata-händelser genereras för var och en av grupperna. Om du vill att en kombinerad aggregering, måste du skapa ett andra icke-partitionerad steg ska aggregeras.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Beräkna max strömningsenheter för ett jobb
Alla icke-partitionerad steg kan tillsammans skala upp till sex strömningsenheter (su) för ett Stream Analytics-jobb. Utöver detta är kan du lägga till 6 su: er för varje partition i en partitionerad steg.
Du kan se några **exempel** i tabellen nedan.

| Söka i data                                               | Max SUs för jobbet |
| --------------------------------------------------- | ------------------- |
| <ul><li>Frågan innehåller ett steg.</li><li>Steget är inte partitionerad.</li></ul> | 6 |
| <ul><li>Inkommande data i dataströmmen har partitionerats med 16.</li><li>Frågan innehåller ett steg.</li><li>Steget är partitionerad.</li></ul> | 96 (6 * 16 partitioner) |
| <ul><li>Frågan innehåller två steg.</li><li>Inget av stegen är partitionerad.</li></ul> | 6 |
| <ul><li>Inkommande data i dataströmmen har partitionerats med 3.</li><li>Frågan innehåller två steg. Det inkommande steget är partitionerad och det andra steget är inte.</li><li>Den <strong>Välj</strong> instruktionen läser från partitionerade indata.</li></ul> | 24 (18 partitionerade anvisningar + 6 för icke-partitionerad steg |

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

När en fråga är partitionerad inkommande händelser bearbetas och aggregeras i en separat partitionsgrupper. Utdata-händelser genereras även för grupper. Partitionering kan orsaka vissa oväntade resultat när den **GROUP BY** fältet är inte Partitionsnyckeln i den inkommande dataströmmen. Till exempel den **TollBoothId** fält i den föregående frågan är inte Partitionsnyckeln för **indata1**. Resultatet är att data från vaktkur nr 1 kan spridas i flera partitioner.

Var och en av de **indata1** partitionerna bearbetas separat av Stream Analytics. Därmed skapas flera poster med antalet bil för samma vaktkur i samma utlösare för rullande fönster. Om inkommande Partitionsnyckeln inte kan ändras, kan det här problemet åtgärdas genom att lägga till ett icke-partition steg aggregerade värden över partitioner, som i följande exempel:

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

## <a name="achieving-higher-throughputs-at-scale"></a>Uppnå högre genomströmning i stor skala

En [embarrassingly parallel](#embarrassingly-parallel-jobs) jobb är nödvändigt men inte tillräckliga för att upprätthålla en högre dataflöde i stor skala. Varje lagringssystemet och dess motsvarande Stream Analytics-utdata har varianter på hur du uppnår bästa möjliga skrivning dataflödet. Som med alla scenarier i skala, det finns några utmaningar som kan lösas genom att använda rätt konfigurationer. Det här avsnittet beskriver konfigurationer för några vanliga utdata och innehåller exempel för tjänstemål enligt datainmatningsfrekvensen 1K, 5K och 10K händelser per sekund.

Följande observationer använda ett Stream Analytics-jobb med tillståndslösa (genomströmning) fråga, en grundläggande JavaScript UDF som skriver till Event Hub, Azure SQL DB eller Cosmos DB.

#### <a name="event-hub"></a>Händelsehubb

|Frekvensen för inmatning (händelser per sekund) | Enheter för strömning | Utdata-resurser  |
|--------|---------|---------|
| 1K     |    1    |  2 DATAFLÖDESENHETER   |
| 5K     |    6    |  6 DATAFLÖDESENHETER   |
| 10 000    |    12   |  10 DATAFLÖDESENHETER  |

Den [Event Hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) lösning skalas linjärt när det gäller strömmande enheter (SU) och dataflöde, vilket gör det mest effektiva och bästa sättet att analysera och strömma data från Stream Analytics. Jobb kan skalas upp till 192 SU, vilket motsvarar ungefär för bearbetning av upp till 200 MB/s eller 19 biljoner händelser per dag.

#### <a name="azure-sql"></a>Azure SQL
|Frekvensen för inmatning (händelser per sekund) | Enheter för strömning | Utdata-resurser  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10 000  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) stöd för skrivning parallellt, kallas ärver partitionering, men det är inte aktiverad som standard. Men kanske att aktivera ärver partitionering, tillsammans med en fullständigt parallell fråga inte tillräckliga för att uppnå högre genomströmning. Genomströmning för skrivning av SQL beror avsevärt på din SQL Azure database configuration och tabellschemat. Den [prestanda som SQL](./stream-analytics-sql-output-perf.md) artikeln innehåller mer information om de parametrar som kan maximera din genomströmning för skrivning. Enligt vad som anges i den [Azure Stream Analytics-utdata till Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artikeln, den här lösningen inte skalas linjärt som en fullständigt parallella pipeline utöver 8 partitioner och kan behöva partitionera om innan SQL-utdata (se [ I](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Premium-SKU: er som krävs för att kunna fortsätta arbeta höga i/o-nivåer tillsammans med tillhörande information från säkerhetskopieringar sker varje par minuter.

#### <a name="cosmos-db"></a>Cosmos DB
|Frekvensen för inmatning (händelser per sekund) | Enheter för strömning | Utdata-resurser  |
|-------|-------|---------|
|  1K   |  3    | 20K RU  |
|  5K   |  24   | 60K RU  |
|  10 000  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) utdata från Stream Analytics har uppdaterats för att använda intern integrering under [kompatibilitetsnivå 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Kompatibilitetsnivån 1.2 kan avsevärt högre dataflöde och minskar RU förbrukning jämfört med 1.1, vilket är standardkompatibilitetsnivån för nya jobb. Lösningen använder cosmos DB-behållare som partitioneras måttgrupperna /deviceId och resten av lösningen konfigureras identiskt.

Alla [direktuppspelning i skala azure-exempel](https://github.com/Azure-Samples/streaming-at-scale) använda en Händelsehubb som skickats av belastning som simulerar testklienter som indata. Varje händelse är ett 1KB JSON-dokument, vilket motsvarar enligt konfigurerade datainmatningsfrekvensen dataflöde priser (1MB/s, 5MB/s och 10MB/s) enkelt. Händelser simulera en IoT-enhet skickar följande JSON-data (i en förkortning) för upp till 1 K-enheter:

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
> Konfigurationerna som kan komma att ändras på grund av de olika komponenterna som används i lösningen. Anpassa exemplen för att passa ditt scenario för en mer tillförlitlig uppskattning.

### <a name="identifying-bottlenecks"></a>Identifiera flaskhalsar

Använda fönstret mått i Azure Stream Analytics-jobb för att identifiera flaskhalsar i din pipeline. Granska **indata/utdata-händelser** för dataflöde och [”vattenstämpel fördröjning”](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) eller **eftersläpande händelser** att se om jobbet är att hålla igång med indata. För Event Hub-mått, leta efter **begränsade begäranden** och justera tröskelvärdet enheter därefter. Cosmos DB-mått, granska **Max konsumerade RU/s per partitionsnyckelintervall** under dataflöde för att se till att partitionen nyckelintervall används ett enhetligt sätt. Azure SQL DB, övervaka **logg-IO** och **CPU**.

## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
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

