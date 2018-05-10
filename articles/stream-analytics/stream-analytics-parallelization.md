---
title: Använd fråga parallellisering och skala i Azure Stream Analytics
description: Den här artikeln beskriver hur du skala Stream Analytics-jobb genom att konfigurera inkommande partitioner, justera frågedefinitionen och ange jobbet enheter för strömning.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 44a7c0721d8a0683162d2219bff0e4a4ecb117e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Utnyttja frågan parallellisering i Azure Stream Analytics
Den här artikeln visar hur du dra nytta av parallellisering i Azure Stream Analytics. Du lär dig att skala Stream Analytics-jobb genom att konfigurera inkommande partitioner och justera frågedefinitionen analytics.
En förutsättning är du kanske vill bekanta dig med begreppet Streaming Unit som beskrivs i [förstå och justera enheter för strömning](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Vilka är delarna av ett Stream Analytics-jobb?
En Stream Analytics-jobbdefinition innehåller indata, en fråge- och utdata. Indata är där jobbet läser från dataströmmen. Frågan används för att omvandla inkommande dataströmmen och utdata är där jobbet skickar resultatet till jobb.  

Ett jobb kräver minst en Indatakällan för strömmande data. Datakälla för dataströmmen inkommande kan lagras i en Azure händelsehubb eller i Azure blob storage. Mer information finns i [introduktion till Azure Stream Analytics](stream-analytics-introduction.md) och [komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partitioner i källor och handfat
Skalning Stream Analytics-jobbet utnyttjar partitioner i in- eller utdata. Partitionering kan du dela upp data i delmängder baserat på en partitionsnyckel. En process som förbrukar data (till exempel ett Streaming Analytics-jobb) kan använda och skriva olika partitioner parallellt, vilket ökar genomströmningen. 

### <a name="inputs"></a>Indata
Alla Azure Stream Analytics-indata kan dra nytta av partitionering:
-   EventHub (måste ange partitionsnyckel explicit med nyckelordet PARTITION BY)
-   IoT-hubb (måste ange partitionsnyckel explicit med nyckelordet PARTITION BY)
-   Blob Storage

### <a name="outputs"></a>Utdata

När du arbetar med Stream Analytics kan du dra nytta av partitionering i utdata:
-   Lagring av Azure Data Lake
-   Azure Functions
-   Azure-tabell
-   BLOB-lagring (kan ange Partitionsnyckeln uttryckligen)
-   CosmosDB (måste uttryckligen ange Partitionsnyckeln)
-   EventHub (måste uttryckligen ange Partitionsnyckeln)
-   IoT-hubb (måste uttryckligen ange Partitionsnyckeln)
-   Service Bus

PowerBI och SQL-informationslager utdata stöd inte för partitionering. Men du kan fortfarande partitionera indata som beskrivs i [i det här avsnittet](#multi-step-query-with-different-partition-by-values) 

Mer information om partitioner finns i följande artiklar:

* [Översikt över Event Hubs funktioner](../event-hubs/event-hubs-features.md#partitions)
* [Data partitionering](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="embarrassingly-parallel-jobs"></a>Embarrassingly parallella jobb
En *embarrassingly parallella* jobbet är den mest skalbara scenario som vi har i Azure Stream Analytics. En partition av indata till en instans av frågan ansluter den till en partition av utdata. Den här parallellitet har följande krav:

1. Om din fråga logik beror på samma nyckel som bearbetas av samma fråga instans, måste du se till att händelserna går till samma partition som indata. Det innebär att informationen om händelsen måste ha för Händelsehubbar eller IoT-hubb i **PartitionKey** värdet uppsättningen. Du kan också använda partitionerade avsändare. För blob storage betyder det att händelserna skickas till samma partition mapp. Om din fråga logik inte kräver samma nyckel som kan bearbetas i samma fråga instans, kan du ignorera det här kravet. Ett exempel på den här logiken skulle vara en enkel fråga väljer project filter.  

2. När data är placerade på inkommande sida måste du kontrollera att frågan är partitionerad. Detta måste du använda **PARTITION BY** i alla steg. Flera steg tillåts, men de måste vara partitionerad med samma nyckel. För närvarande partitionsnyckel måste anges till **PartitionId** för jobbet ska vara fullständigt parallellt.  

3. De flesta av våra utdata kan dra nytta av partitionering, men om du använder en output-typ som inte stöder partitionering jobbet kommer inte att fullständigt parallellt. Referera till den [utdata avsnittet](#outputs) för mer information.

4. Antalet inkommande partitioner måste vara lika med antalet partitioner för utdata. BLOB storage utdata kan hantera partitioner och ärver partitioneringsschema av överordnad frågan. När en partitionsnyckel för Blob storage anges data är partitionerad per inkommande partition därför är resultatet fortfarande helt parallellt. Här följer exempel på partitionen värden som tillåter ett fullständigt parallella jobb:

   * 8 event hub inkommande partitioner och 8 händelsehubb utdata partitioner
   * 8 event hub inkommande partitioner och blob storage-utdata
   * 8 händelsehubb indata partitioner och blob storage utdata partitionerats med ett anpassat fält med godtycklig kardinalitet
   * 8 blob storage inkommande partitioner och blob storage-utdata
   * 8 blob storage inkommande partitioner och 8 event hub utdata partitioner

I följande avsnitt beskrivs några exempelscenarier som embarrassingly parallella.

### <a name="simple-query"></a>Exempelfråga

* Indata: Event hub med 8 partitioner
* Utdata: Event hub med 8 partitioner

Fråga:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Den här frågan är ett enkelt filter. Därför behöver vi inte bry dig om partitionering indata som skickas till händelsehubben. Observera att frågan inkluderar **PARTITION av PartitionId**, så att den uppfyller krav #2 från tidigare. Vi behöver konfigurera event hub utdata i jobbet har den partition inställt på för utdata, **PartitionId**. En senaste kontrollen är att se till att antalet inkommande partitioner är lika med antalet partitioner för utdata.

### <a name="query-with-a-grouping-key"></a>Fråga med en grupperingsnyckel för

* Indata: Event hub med 8 partitioner
* Utdata: Blob storage

Fråga:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Den här frågan har en gruppering. Därför måste de händelser som grupperas tillsammans skickas till samma partition i Händelsehubben. Eftersom vi gruppen av TollBoothID i det här exemplet ska vi till att TollBoothID används som partitionsnyckel när händelser skickas till Händelsehubben. Vi kan använda i ASA, **PARTITION av PartitionId** att ärva från den här partitionsschema och aktivera fullständig parallellisering. Eftersom utdata är blob storage, behöver vi inte bry dig om hur du konfigurerar en partitionsnyckelvärde, enligt kravet #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exempel på scenarier som är *inte* embarrassingly parallellt

Vi har visat vissa embarrassingly parallella scenarier i föregående avsnitt. I det här avsnittet diskuterar vi scenarier som inte uppfyller alla krav för att vara embarrassingly parallella. 

### <a name="mismatched-partition-count"></a>Antal matchningar partitioner
* Indata: Event hub med 8 partitioner
* Utdata: Event hub med 32 partitioner

I det här fallet det spelar ingen roll frågan är. Om antalet inkommande partitioner inte matchar antalet partitioner utdata, inte topologin embarrassingly parallellt. + men vi kan fortfarande få vissa nivå eller parallellisering.

### <a name="query-using-non-partitioned-output"></a>Fråga med partitionerade utdata
* Indata: Event hub med 8 partitioner
* Utdata: PowerBI

PowerBI utdata stöder för närvarande inte partitionering. Det här scenariot är därför inte embarrassingly parallellt.

### <a name="multi-step-query-with-different-partition-by-values"></a>Flera steg fråga med olika PARTITION BY-värden
* Indata: Event hub med 8 partitioner
* Utdata: Event hub med 8 partitioner

Fråga:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Som du ser det andra steget använder **TollBoothId** som partitionsnyckel. Det här steget är inte samma som det första steget och det därför kräver oss att göra en blandad. 

I föregående exempel visas vissa Stream Analytics-jobb som överensstämmer med (inte eller) en embarrassingly parallella topologi. Om de uppfyller har risken för maximal skala. För jobb som inte passar något av dessa profiler vägledning skalning kommer att vara tillgänglig i framtida uppdateringar. Använd allmän vägledning för tillfället i följande avsnitt.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Beräkna det högsta strömningsenheter för ett jobb
Det totala antalet strömningsenheter som kan användas av ett Stream Analytics-jobb beror på antalet steg i frågan som definierats för jobbet och antalet partitioner för varje steg.

### <a name="steps-in-a-query"></a>Stegen i en fråga
En fråga kan ha en eller flera steg. Varje steg finns en underfråga som definieras av den **WITH** nyckelord. Den fråga som ligger utanför den **WITH** nyckelordet (enbart en fråga) också räknas som ett steg som den **Välj** instruktionen i följande fråga:

Fråga:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Den här frågan har två steg.

> [!NOTE]
> Den här frågan diskuteras i detalj senare i artikeln.
>  

### <a name="partition-a-step"></a>Partitionera ett steg
Partitionering ett steg kräver följande villkor:

* Indatakällan vara partitionerade. 
* Den **Välj** -uttrycket för frågan måste läsa från en partitionerad Indatakällan.
* Frågan i steget som måste ha den **PARTITION BY** nyckelord.

När en fråga är partitionerad inkommande händelser är bearbetade och aggregerade i separat partitionsgrupper och utdata händelser genereras för grupper. Om du vill att en kombinerad mängd, måste du skapa ett andra partitionerade steg ska aggregeras.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Beräkna det högsta antalet strömningsenheter för ett jobb
Alla åtgärder för partitionerade kan tillsammans skalas upp till sex enheter för strömning (SUs) för ett Stream Analytics-jobb. Utöver detta kan du lägga till 6 SUs för varje partition i en partitionerad steg.
Du kan se några **exempel** i tabellen nedan.

| Fråga                                               | Max SUs för jobbet |
| --------------------------------------------------- | ------------------- |
| <ul><li>Frågan innehåller ett steg.</li><li>Steget är inte partitionerad.</li></ul> | 6 |
| <ul><li>Inkommande dataströmmen har partitionerats med 16.</li><li>Frågan innehåller ett steg.</li><li>Steget är partitionerad.</li></ul> | 96 (6 * 16 partitioner) |
| <ul><li>Frågan innehåller två steg.</li><li>Inget av stegen är partitionerad.</li></ul> | 6 |
| <ul><li>Inkommande dataströmmen är partitionerad med 3.</li><li>Frågan innehåller två steg. Steget indata är partitionerad och det andra steget är inte.</li><li>Den <strong>Välj</strong> instruktionen läser från partitionerade indata.</li></ul> | 24 (18 partitionerade anvisningar + 6 partitionerade anvisningar |

### <a name="examples-of-scaling"></a>Exempel på skalning

Följande fråga beräknar antalet bilar inom en minut tre period gå via en avgift station som har tre tollbooths. Den här frågan kan skalas upp till sex SUs.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Om du vill använda flera SUs för frågan, måste både den inkommande dataströmmen och frågan partitioneras. Eftersom data dataströmmen partitionen är satt till 3, kan följande ändrade fråga skalas upp till 18 SUs:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

När en fråga är partitionerad behandlas inkommande händelser och aggregeras i en separat partitionsgrupper. Utdatahändelserna genereras även för grupper. Partitionering kan orsaka vissa oväntade resultat när den **GROUP BY** fältet är inte partitionsnyckel i den inkommande dataströmmen. Till exempel den **TollBoothId** fält i den föregående frågan är inte Partitionsnyckeln för **Input1**. Resultatet är att data från vaktkur #1 kan spridas i flera partitioner.

Var och en av de **Input1** partitionerna bearbetas separat av Stream Analytics. Därmed skapas flera poster antalet bil för samma vaktkur i samma rullande fönster. Om inkommande Partitionsnyckeln inte kan ändras, kan detta problem åtgärdas genom att lägga till ett icke-partition steg sammanställda värden över partitioner, som i följande exempel:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Den här frågan kan skalas till 24 SUs.

> [!NOTE]
> Om du ansluter till två dataströmmar, kontrollerar du att strömmar partitioneras av Partitionsnyckeln för kolumnen som används för att skapa kopplingarna. Kontrollera också att du har samma antal partitioner i båda dataströmmar.
> 
> 





## <a name="get-help"></a>Få hjälp
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

