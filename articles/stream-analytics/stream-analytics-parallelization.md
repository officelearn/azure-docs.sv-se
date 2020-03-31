---
title: Använda frågeallalualisering och skala i Azure Stream Analytics
description: I den här artikeln beskrivs hur du skalar Stream Analytics-jobb genom att konfigurera indatapartitioner, justera frågedefinitionen och ange jobbströmningsenheter.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 31ac43ec796d305b8a8f4b62ea09481e262b6b3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256988"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Utnyttja frågeallallalisering i Azure Stream Analytics
Den här artikeln visar hur du drar nytta av parallellisering i Azure Stream Analytics. Du lär dig hur du skalar Stream Analytics-jobb genom att konfigurera indatapartitioner och justera definition av analytics-frågor.
Som en förutsättning kanske du vill känna till begreppet streamingenhet som beskrivs i [Förstå och justera strömningsenheter](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Vilka är de delar av ett Stream Analytics-jobb?
En Stream Analytics-jobbdefinition innehåller indata, en fråga och utdata. Indata är där jobbet läser dataströmmen från. Frågan används för att omvandla dataindataströmmen och utdata är där jobbet skickar jobbresultaten till.

Ett jobb kräver minst en indatakälla för dataströmning. Indatakällan för dataström kan lagras i en Azure-händelsenav eller i Azure blob-lagring. Mer information finns i [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md) och Kom igång med Azure Stream [Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partitioner i källor och sänkor
Skalning av ett Stream Analytics-jobb utnyttjar partitioner i indata eller utdata. Med partitionering kan du dela upp data i delmängder baserat på en partitionsnyckel. En process som använder data (till exempel ett Streaming Analytics-jobb) kan använda och skriva olika partitioner parallellt, vilket ökar dataflödet. 

### <a name="inputs"></a>Indata
Alla Azure Stream Analytics-indata kan dra nytta av partitionering:
-   EventHub (måste ange partitionsnyckeln explicit med nyckelordet PARTITION BY)
-   IoT Hub (måste ställa in partitionsnyckeln uttryckligen med nyckelordet PARTITION BY)
-   Blob Storage

### <a name="outputs"></a>Utdata

När du arbetar med Stream Analytics kan du dra nytta av partitionering i utdata:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure-tabell
-   Blob-lagring (kan uttryckligen ange partitionsnyckeln)
-   Cosmos DB (måste ställa in partitionsnyckeln explicit)
-   Event Hubs (måste ange partitionsnyckeln explicit)
-   IoT Hub (måste ange partitionsnyckeln explicit)
-   Service Bus
- SQL och SQL Data Warehouse med valfri partitionering: se mer information på [sidan Utdata till Azure SQL Database](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Power BI stöder inte partitionering. Du kan dock fortfarande partitionera indata enligt beskrivningen i [det här avsnittet](#multi-step-query-with-different-partition-by-values) 

Mer information om partitioner finns i följande artiklar:

* [Översikt över Event Hubs-funktioner](../event-hubs/event-hubs-features.md#partitions)
* [Datapartitionering](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Pinsamt parallella jobb
Ett *pinsamt parallellt* jobb är det mest skalbara scenariot vi har i Azure Stream Analytics. Den ansluter en partition av indata till en instans av frågan till en partition av utdata. Denna parallellism har följande krav:

1. Om frågelogiken beror på att samma nyckel bearbetas av samma frågeinstans måste du se till att händelserna hamnar på samma partition i indata. För eventhubbar eller IoT-hubb innebär detta att händelsedata måste ha värdet **PartitionKey** inställt. Du kan också använda partitionerade avsändare. För blob-lagring innebär det att händelserna skickas till samma partitionsmapp. Om frågelogiken inte kräver samma nyckel som ska bearbetas av samma frågeinstans kan du ignorera det här kravet. Ett exempel på den här logiken är en enkel fråga om select-project-filter.  

2. När data har lagts ut på indatasidan måste du se till att frågan är partitionerad. Detta kräver att du använder **PARTITION BY** i alla steg. Flera steg är tillåtna, men alla måste partitioneras av samma nyckel. Under kompatibilitetsnivå 1.0 och 1.1 måste partitioneringsnyckeln ställas in på **PartitionId** för att jobbet ska vara helt parallellt. För jobb med kompatibilitetsnivå 1.2 och högre kan anpassad kolumn anges som partitionsnyckel i indatainställningarna och jobbet paralelliseras automatiskt även utan PARTITION BY-sats. För händelsehubbutdata måste egenskapen "Partition key column" ställas in för att använda "PartitionId".

3. De flesta av våra utdata kan dra nytta av partitionering, men om du använder en utdatatyp som inte stöder partitionering ditt jobb kommer inte att vara helt parallellt. För event hub-utdata kontrollerar du att **partitionsnyckelkolumnen** är samma som frågepartitionsnyckeln. Mer information finns i [utdataavsnittet.](#outputs)

4. Antalet indatapartitioner måste vara lika med antalet utdatapartitioner. Blob lagring utdata kan stödja partitioner och ärver partitionering schemat för uppströms frågan. När en partitionsnyckel för Blob-lagring anges partitioneras data per indatapartition, vilket innebär att resultatet fortfarande är helt parallellt. Här är exempel på partitionsvärden som tillåter ett helt parallellt jobb:

   * 8 händelsehubbindatapartitioner och 8 händelsehubbutdatapartitioner
   * 8 händelsehubbindatapartitioner och bloblagringsutdata
   * 8 händelsehubbindatapartitioner och bloblagringsutdata som partitioneras av ett anpassat fält med godtycklig kardinalitet
   * 8 blob lagringsindatapartitioner och blob lagring utdata
   * 8 blob lagringsindatapartitioner och 8 händelsehubbutdatapartitioner

I följande avsnitt beskrivs några exempelscenarier som är pinsamt parallella.

### <a name="simple-query"></a>Exempelfråga

* Indata: Händelsehubb med 8 partitioner
* Utdata: Händelsehubb med 8 partitioner ("Partitionnyckelkolumn" måste ställas in för att använda "PartitionId")

Fråga:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Den här frågan är ett enkelt filter. Därför behöver vi inte oroa oss för att partitionera indata som skickas till händelsehubben. Observera att jobb med kompatibilitetsnivå före 1.2 måste innehålla **PARTITION BY PartitionId-satsen,** så att den uppfyller kraven #2 från tidigare. För utdata måste vi konfigurera händelsehubbutdata i jobbet så att partitionsnyckeln är inställd **på PartitionId**. En sista kontroll är att se till att antalet indatapartitioner är lika med antalet utdatapartitioner.

### <a name="query-with-a-grouping-key"></a>Fråga med en grupperingsnyckel

* Indata: Händelsehubb med 8 partitioner
* Utdata: Blob-lagring

Fråga:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Den här frågan har en grupperingsnyckel. Därför måste de händelser som grupperas tillsammans skickas till samma Event Hub-partition. Eftersom vi i det här exemplet grupperar av TollBoothID bör vi vara säkra på att TollBoothID används som partitionsnyckel när händelserna skickas till Event Hub. Sedan i ASA kan vi använda **PARTITION BY PartitionId** att ärva från denna partition schema och aktivera fullständig parallellisering. Eftersom utdata är blob-lagring behöver vi inte oroa oss för att konfigurera ett partitionsnyckelvärde, enligt krav #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exempel på scenarier som *inte* är pinsamt parallella

I föregående avsnitt visade vi några pinsamt parallella scenarier. I det här avsnittet diskuterar vi scenarier som inte uppfyller alla krav för att vara pinsamt parallella. 

### <a name="mismatched-partition-count"></a>Antal inkompatibla partitioner
* Indata: Händelsehubb med 8 partitioner
* Utdata: Händelsehubb med 32 partitioner

I det här fallet spelar det ingen roll vad frågan är. Om antalet indatapartitioner inte matchar antalet utdatapartitioner är topologin inte pinsamt parallell.+ Men vi kan fortfarande få en viss nivå eller parallellisering.

### <a name="query-using-non-partitioned-output"></a>Fråga med icke-partitionerad utdata
* Indata: Händelsehubb med 8 partitioner
* Utgång: Power BI

Power BI-utdata stöder för närvarande inte partitionering. Därför är detta scenario inte pinsamt parallellt.

### <a name="multi-step-query-with-different-partition-by-values"></a>Flerstegsfråga med olika PARTITION BY-värden
* Indata: Händelsehubb med 8 partitioner
* Utdata: Händelsehubb med 8 partitioner

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

Som du kan se använder det andra steget **TollBoothId** som partitioneringsnyckel. Detta steg är inte samma sak som det första steget, och det kräver därför att vi gör en shuffle. 

De föregående exemplen visar några Stream Analytics-jobb som överensstämmer med (eller inte) en pinsamt parallell topologi. Om de överensstämmer, de har potential för maximal skala. För jobb som inte passar någon av dessa profiler är skalningsvägledning tillgänglig i framtida uppdateringar. Använd för tillfället den allmänna vägledningen i följande avsnitt.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Kompatibilitetsnivå 1.2 - Flerstegsfråga med olika PARTITION BY-värden 
* Indata: Händelsehubb med 8 partitioner
* Utdata: Händelsehubb med 8 partitioner ("Partition nyckelkolumn" måste ställas in för att använda "TollBoothId")

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

Kompatibilitetsnivå 1.2 möjliggör parallell körning av frågor som standard. Frågan från föregående avsnitt kommer till exempel att partitioneras så länge kolumnen "TollBoothId" anges som indatapartitionsnyckel. PARTITION BY PartitionId-satsen krävs inte.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Beräkna de maximala strömningsenheterna för ett jobb
Det totala antalet strömningsenheter som kan användas av ett Stream Analytics-jobb beror på antalet steg i frågan som definierats för jobbet och antalet partitioner för varje steg.

### <a name="steps-in-a-query"></a>Steg i en fråga
En fråga kan ha ett eller flera steg. Varje steg är en underkvent som definieras av nyckelordet **WITH.** Frågan som ligger utanför nyckelordet **WITH** (endast en fråga) räknas också som ett steg, till exempel **SELECT-satsen** i följande fråga:

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
> Den här frågan beskrivs mer i detalj senare i artikeln.
>  

### <a name="partition-a-step"></a>Partitionera ett steg
Partitionering av ett steg kräver följande villkor:

* Indatakällan måste partitioneras. 
* **SELECT-satsen** för frågan måste läsas från en partitionerad indatakälla.
* Frågan i steget måste ha nyckelordet **PARTITION BY.**

När en fråga partitioneras bearbetas och aggregeras indatahändelserna i separata partitionsgrupper och utdatahändelser genereras för var och en av grupperna. Om du vill ha en kombinerad mängd måste du skapa ett andra icke-partitionerat steg för att aggregera.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Beräkna maxstreamingenheter för ett jobb
Alla steg som inte är partitionerade tillsammans kan skala upp till sex strömningsenheter (SUs) för ett Stream Analytics-jobb. Utöver detta kan du lägga till 6 SUs för varje partition i ett partitionerat steg.
Du kan se några **exempel** i tabellen nedan.

| Söka i data                                               | Max SUs för jobbet |
| --------------------------------------------------- | ------------------- |
| <ul><li>Frågan innehåller ett steg.</li><li>Steget är inte partitionerat.</li></ul> | 6 |
| <ul><li>Indataströmmen partitioneras med 16.</li><li>Frågan innehåller ett steg.</li><li>Steget är partitionerat.</li></ul> | 96 (6 * 16 partitioner) |
| <ul><li>Frågan innehåller två steg.</li><li>Inget av stegen är partitionerat.</li></ul> | 6 |
| <ul><li>Indataströmmen partitioneras av 3.</li><li>Frågan innehåller två steg. Indatasteget är partitionerat och det andra steget är det inte.</li><li><strong>SELECT-satsen</strong> läser från den partitionerade indata.</li></ul> | 24 (18 för partitionerade steg + 6 för icke-partitionerade steg |

### <a name="examples-of-scaling"></a>Exempel på skalning

Följande fråga beräknar antalet bilar inom ett treminutersfönster som går genom en vägtullstation som har tre vägtullar. Den här frågan kan skalas upp till sex SUs.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Om du vill använda fler SUs för frågan måste både indataströmmen och frågan partitioneras. Eftersom dataströmspartitionen är inställd på 3 kan följande ändrade fråga skalas upp till 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

När en fråga partitioneras bearbetas och aggregeras indatahändelserna i separata partitionsgrupper. Utdatahändelser genereras också för var och en av grupperna. Partitionering kan orsaka oväntade resultat när fältet **GROUP BY** inte är partitionsnyckeln i indataströmmen. **Fältet TollBoothId** i föregående fråga är till exempel inte partitionsnyckeln **för Input1**. Resultatet är att data från TollBooth #1 kan spridas i flera partitioner.

Var och en av **Input1-partitionerna** bearbetas separat av Stream Analytics. Som ett resultat kommer flera register över antalet bilar för samma vägtullar i samma tumlande fönster att skapas. Om indatapartitionsnyckeln inte kan ändras kan det här problemet åtgärdas genom att lägga till ett steg som inte är partitioner i aggregerade värden över partitioner, som i följande exempel:

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
> Om du ansluter till två strömmar kontrollerar du att strömmarna är partitionerade efter partitionsnyckeln i kolumnen som du använder för att skapa kopplingarna. Se också till att du har samma antal partitioner i båda strömmarna.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Uppnå högre dataflöde i stor skala

Ett [pinsamt parallellt](#embarrassingly-parallel-jobs) jobb är nödvändigt men inte tillräckligt för att upprätthålla en högre genomströmning i stor skala. Varje lagringssystem och motsvarande Stream Analytics-utdata har variationer i hur du uppnår bästa möjliga skrivdataflöde. Som med alla at-scale scenario, det finns vissa utmaningar som kan lösas med hjälp av rätt konfigurationer. I det här avsnittet beskrivs konfigurationer för några vanliga utdata och exempel på hur du kan upprätthålla inmatningshastigheter på 1K-, 5K- och 10K-händelser per sekund.

Följande observationer använder ett Stream Analytics-jobb med tillståndslös (passthrough)-fråga, en grundläggande JavaScript UDF som skriver till Event Hub, Azure SQL DB eller Cosmos DB.

#### <a name="event-hub"></a>Händelsehubb

|Intagshastighet (händelser per sekund) | Strömningsenheter | Utdataresurser  |
|--------|---------|---------|
| 1K     |    1    |  2 TU   |
| 5 000     |    6    |  6 TU (TT)   |
| 10 000    |    12   |  10 TU (TT)  |

[Event Hub-lösningen](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) skalas linjärt när det gäller strömningsenheter (SU) och dataflöde, vilket gör den till det mest effektiva och högpresterande sättet att analysera och strömma data från Stream Analytics. Jobb kan skalas upp till 192 SU, vilket ungefär kan översättas till bearbetning upp till 200 MB / s, eller 19 biljoner händelser per dag.

#### <a name="azure-sql"></a>Azure SQL
|Intagshastighet (händelser per sekund) | Strömningsenheter | Utdataresurser  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5 000   |   18 |  P4   |
|    10 000  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) stöder att skriva parallellt, så kallad Inherit Partitioning, men det är inte aktiverat som standard. Det kanske dock inte räcker att aktivera Inherit Partitioning, tillsammans med en helt parallell fråga, för att uppnå högre dataflöde. SQL-skrivdatadatadatadatadatadataflödet beror i hög grad på konfigurationen och tabellschemat för SQL Azure-databasen. [Sql Output Performance-artikeln](./stream-analytics-sql-output-perf.md) innehåller mer information om de parametrar som kan maximera skrivflödet. Som anges i Azure [Stream Analytics-utdata till Azure SQL Database-artikeln](./stream-analytics-sql-output-perf.md#azure-stream-analytics) skalas den här lösningen inte linjärt som en helt parallell pipeline utöver 8 partitioner och kan behöva partitioneras om innan SQL-utdata (se [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Premium SKU:er behövs för att upprätthålla höga IO-priser tillsammans med omkostnader från log-säkerhetskopior som sker med några minuters mellanrum.

#### <a name="cosmos-db"></a>Cosmos DB
|Intagshastighet (händelser per sekund) | Strömningsenheter | Utdataresurser  |
|-------|-------|---------|
|  1K   |  3    | 20K RU  |
|  5 000   |  24   | 60K RU  |
|  10 000  |  48   | 120K RU |

[Cosmos](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) DB-utdata från Stream Analytics har uppdaterats för att använda inbyggd integrering under [kompatibilitetsnivå 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Kompatibilitetsnivå 1.2 möjliggör betydligt högre genomströmning och minskar RU-förbrukningen jämfört med 1,1, vilket är standardkompatibilitetsnivån för nya jobb. Lösningen använder CosmosDB-behållare som partitioneras på /deviceId och resten av lösningen är identiskt konfigurerad.

Alla [Azure-exemplen för direktuppspelning på skalning](https://github.com/Azure-Samples/streaming-at-scale) använder en eventnav som matas genom att simulera testklienter som indata. Varje indatahändelse är ett 1KB JSON-dokument, som översätter konfigurerade inmatningshastigheter till dataflödeshastigheter (1 MB/s, 5 MB/s och 10 MB/s) enkelt. Händelser simulerar en IoT-enhet som skickar följande JSON-data (i förkortad form) för upp till 1K-enheter:

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
> Konfigurationerna kan komma att ändras på grund av de olika komponenter som används i lösningen. Om du vill ha en mer exakt uppskattning anpassar du exemplen så att de passar ditt scenario.

### <a name="identifying-bottlenecks"></a>Identifiera flaskhalsar

Använd fönstret Mått i ditt Azure Stream Analytics-jobb för att identifiera flaskhalsar i pipelinen. Granska **indata-/utdatahändelser** för dataflöde och ["Watermark Delay"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) eller **Backlogged Events** för att se om jobbet håller jämna steg med indatahastigheten. Leta efter **begränsade begäranden** för händelsehubbar och justera tröskelvärdena i enlighet med detta. För Cosmos DB-mått bör du granska **Max förbrukade RU/s per partitionsnyckelintervall** under Dataflöde för att säkerställa att dina partitionsnyckelintervall förbrukas på ett enhetligt sätt. För Azure SQL DB övervakar du **Log IO** och **CPU**.

## <a name="get-help"></a>Få hjälp

Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

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

