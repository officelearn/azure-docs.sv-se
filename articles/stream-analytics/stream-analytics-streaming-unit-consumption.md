---
title: Strömmande enheter i Azure Stream Analytics
description: I den här artikeln beskrivs inställningen för strömningsenheter och andra faktorer som påverkar prestanda i Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 397e455c8b6a1097e2a32473036e1acd2bbdf2eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267357"
---
# <a name="understand-and-adjust-streaming-units"></a>Förstå och justera direktuppspelningsenheter

Strömningsenheter (SUs) representerar de datorresurser som allokeras för att köra ett Stream Analytics-jobb. Ju fler SU:er, desto fler processor- och minnesresurser allokeras för jobbet. Med den här kapaciteten kan du fokusera på frågelogiken och abstrakta behovet av att hantera maskinvaran för att köra stream analytics-jobbet i tid.

För att minimera svarstiderna vid bearbetningen av dataströmmar utför Azure Stream Analytics-jobb all bearbetning i minnet. När minnet tar misslyckas strömningsjobbet. För ett produktionsjobb är det därför viktigt att övervaka ett strömningsjobbs resursanvändning och se till att det finns tillräckligt med resurser för att hålla jobben igång dygnet 24/7.

Su% utnyttjandemått, som sträcker sig från 0% till 100%, beskriver minnesförbrukningen för din arbetsbelastning. För ett direktuppspelningsjobb med minimalt fotavtryck är det här måttet vanligtvis mellan 10 % och 20 %. Om SU% utnyttjande är låg och indatahändelser får eftersläpning, kräver din arbetsbelastning sannolikt mer beräkningsresurser, vilket kräver att du ökar antalet SUs. Det är bäst att hålla SU-måttet under 80% för att ta hänsyn till enstaka spikar. Microsoft rekommenderar att du ställer in en avisering på 80 % SU-utnyttjande för att förhindra resursutmattning. Mer information finns i [Självstudiekurs: Konfigurera aviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurera Stream Analytics-strömenheter (SUs)
1. Logga in på [Azure-portalen](https://portal.azure.com/)

2. Leta reda på det Stream Analytics-jobb som du vill skala och öppna det i listan över resurser. 

3. Välj **Skala**under rubriken **Konfigurera** på jobbsidan . 

    ![Jobbkonfiguration för Azure Portal Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. Använd skjutreglaget för att ställa in SUs för jobbet. Observera att du är begränsad till specifika SU-inställningar. 
5. Du kan ändra antalet SUs som tilldelats jobbet även när det körs. Detta är inte möjligt om jobbet använder en [icke-partitionerad utdata](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) eller har [en flerstegsfråga med olika PARTITION BY-värden](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values). Du kanske är begränsad till att välja från en uppsättning SU-värden när jobbet körs. 

## <a name="monitor-job-performance"></a>Övervaka jobbets prestanda
Med Hjälp av Azure-portalen kan du spåra dataflödet för ett jobb:

![Azure Stream Analytics övervakar jobb][img.stream.analytics.monitor.job]

Beräkna arbetsbelastningens förväntade dataflöde. Om dataflödet är mindre än förväntat justerar du indatapartitionen, ställer in frågan och lägger till SUs i jobbet.

## <a name="how-many-sus-are-required-for-a-job"></a>Hur många SU:er behövs för ett jobb?

Att välja antalet nödvändiga SUs för ett visst jobb beror på partitionskonfigurationen för indata och frågan som har definierats i jobbet. På **sidan Skala** kan du ange rätt antal SUs. Det är en bästa praxis att fördela fler SUs än nödvändigt. Stream Analytics-bearbetningsmotorn optimerar för svarstid och dataflöde på bekostnad av allokering av ytterligare minne.

I allmänhet är det bästa sättet att börja med 6 SUs för frågor som inte använder **PARTITION BY**. Bestäm sedan sweet spot genom att använda en trial and error-metod där du ändrar antalet SUs när du har klarat representativa mängder data och undersöker su% utnyttjandemåttet. Det maximala antalet strömningsenheter som kan användas av ett Stream Analytics-jobb beror på antalet steg i frågan som definierats för jobbet och antalet partitioner i varje steg. Du kan läsa mer om gränserna [här](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Mer information om hur du väljer rätt antal SUs finns på den här sidan: [Skala Azure Stream Analytics-jobb för att öka dataflödet](stream-analytics-scale-jobs.md)

> [!Note]
> Att välja hur många SUs som krävs för ett visst jobb beror på partitionskonfigurationen för indata och på frågan som definierats för jobbet. Du kan välja upp till din kvot i SUs för ett jobb. Som standard har varje Azure-prenumeration en kvot på upp till 500 SUs för alla analysjobb i en viss region. Om du vill öka SUs för dina prenumerationer utöver den här kvoten kontaktar du [Microsoft Support](https://support.microsoft.com). Giltiga värden för SUs per jobb är 1, 3, 6 och uppåt i steg om 6.

## <a name="factors-that-increase-su-utilization"></a>Faktorer som ökar den procentuella användningen av SU:er 

Tidsmässiga (tidsorienterade) frågeelement är kärnuppsättningen av tillståndskänsliga operatorer som tillhandahålls av Stream Analytics. Stream Analytics hanterar tillståndet för dessa åtgärder internt för användarens räkning, genom att hantera minnesförbrukning, kontrollpunkt för återhämtning och tillståndsåterställning under tjänstuppgraderingar. Även om Stream Analytics hanterar lägena fullt ut finns det ett antal rekommendationer för bästa praxis som användarna bör överväga.

Observera att ett jobb med komplex frågelogik kan ha hög SU%-användning även när det inte kontinuerligt tar emot indatahändelser. Detta kan inträffa efter en plötslig ökning av indata- och utdatahändelser. Jobbet kan fortsätta att upprätthålla tillståndet i minnet om frågan är komplex.

SU% utnyttjande kan plötsligt sjunka till 0 under en kort period innan de kommer tillbaka till förväntade nivåer. Detta händer på grund av tillfälliga fel eller systeminitierade uppgraderingar. Att öka antalet strömningsenheter för ett jobb kanske inte minskar SU%-användningen om frågan inte är [helt parallell](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

När du jämför utnyttjandet över en tidsperiod använder du [händelsehastighetsmått](stream-analytics-monitoring.md). InputEvents och OutputEvents-mått visar hur många händelser som lästes och bearbetades. Det finns mått som anger antalet felhändelser också, till exempel deserialiseringsfel. När antalet händelser per tidsenhet ökar ökar SU% i de flesta fall.

## <a name="stateful-query-logicin-temporal-elements"></a>Tillståndskänslig frågelogik i temporala element
En av de unika funktionerna i Azure Stream Analytics-jobbet är att utföra tillståndskänslig bearbetning, till exempel fönsterade aggregat, temporala kopplingar och temporala analytiska funktioner. Var och en av dessa operatörer håller tillståndsinformation.Den maximala fönsterstorleken för dessa frågeelement är sju dagar. 

Temporal fönsterkonceptet visas i flera Stream Analytics-frågeelement:
1. Fönsterade aggregat: GROUP BY av Tumbling, Hopping och Sliding fönster

2. Temporala kopplingar: JOIN med funktionen DATEDIFF

3. Temporala analytiska funktioner: ISFIRST, LAST och LAG MED LIMIT DURATION

Följande faktorer påverkar det minne som används (en del av datamått för direktuppspelningsenheter) av Stream Analytics-jobb:

## <a name="windowed-aggregates"></a>Fönsterade aggregat
Det minne som förbrukas (tillståndsstorlek) för en fönsterad aggregat är inte alltid direkt proportionell mot fönsterstorleken. I stället är det minne som förbrukas proportionellt mot kardinaliteten för data, eller antalet grupper i varje tidsfönster.


I följande fråga är till exempel `clusterid` det tal som är associerat med frågans kardinalitet. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

För att minska eventuella problem som orsakas av hög kardinalitet i föregående `clusterid`fråga kan du skicka händelser till Event Hub partitionerad av och skala ut frågan genom att låta systemet bearbeta varje indatapartition separat med **partition BY** som visas i exemplet nedan:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

När frågan är utpartitionerad sprids den ut över flera noder. Som ett resultat minskas antalet `clusterid` värden som kommer in i varje nod vilket minskar gruppens kardinalitet för operatör. 

Event Hub-partitioner bör partitioneras av gruppningsnyckeln för att undvika behovet av ett minskande steg. Mer information finns i [översikt över händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Temporala kopplingar
Det minne som förbrukas (tillståndsstorlek) för en temporal koppling är proportionellt mot antalet händelser i det tidsmässiga vicka rummet för kopplingen, vilket är händelseinmatningshastighet multiplicerat med vicka rumsstorlek. Med andra ord är det minne som förbrukas av kopplingar proportionellt mot datumdifftidintervallet multiplicerat med genomsnittlig händelsehastighet.

Antalet omatchade händelser i kopplingen påverkar minnet utnyttjande för frågan. Följande fråga söker efter reklamannonser som genererar klick:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

I det här exemplet är det möjligt att många annonser visas och få människor klickar på den och det krävs för att hålla alla händelser i tidsfönstret. Förbrukat minne beror på tidsperiodens längd och händelsens frekvens. 

Om du vill åtgärda detta skickar du händelser till Event Hub som partitioneras av kopplingsnycklarna (ID i det här fallet) och skalar ut frågan genom att låta systemet bearbeta varje indatapartition separat med **partitionen SOM** visas:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

När frågan är utpartitionerad sprids den ut över flera noder. Som ett resultat av antalet händelser som kommer in i varje nod minskas vilket minskar storleken på det tillstånd som hålls i kopplingsfönstret. 

## <a name="temporal-analytic-functions"></a>Temporala analytiska funktioner
Det minne som förbrukas (tillståndsstorlek) för en temporal analytisk funktion är proportionellt mot händelsehastigheten multiplicera med varaktigheten.Det minne som förbrukas av analytiska funktioner är inte proportionellt mot fönsterstorleken, utan partitionsantalet i varje tidsfönster.

Reparationen liknar temporal koppling. Du kan skala ut frågan med **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Ur funktionsbuffert 
Användaren kan konfigurera buffertstorleken i oordning i konfigurationsfönstret För händelseordning. Bufferten används för att hålla indata under fönstrets varaktighet och ändra ordning på dem. Buffertens storlek är proportionell mot händelseinmatningshastigheten multipliceras med den i slutordningens fönsterstorlek. Standardfönstrets storlek är 0. 

Om du vill åtgärda spill av bufferten i oordning skalar du ut frågan med **PARTITION BY**. När frågan är utpartitionerad sprids den ut över flera noder. Därför minskar antalet händelser som kommer in i varje nod vilket minskar antalet händelser i varje beställningsbuffert. 

## <a name="input-partition-count"></a>Antal indatapartitioner 
Varje indatapartition för en jobbindata har en buffert. Ju större antal indatapartitioner, desto mer resurs förbrukar jobbet. För varje streamingenhet kan Azure Stream Analytics bearbeta ungefär 1 MB/s indata. Därför kan du optimera genom att matcha antalet Stream Analytics-enheter med antalet partitioner i händelsehubben. 

Vanligtvis är ett jobb som konfigurerats med en strömningsenhet tillräckligt för en eventnav med två partitioner (vilket är det minsta för Event Hub). Om händelsehubben har fler partitioner förbrukar stream analytics-jobbet mer resurser, men använder inte nödvändigtvis det extra dataflödet som tillhandahålls av Event Hub. 

För ett jobb med 6 strömningsenheter kan du behöva 4 eller 8 partitioner från eventhubben. Undvik dock för många onödiga partitioner eftersom det orsakar överdriven resursanvändning. Till exempel en eventhubb med 16 partitioner eller större i ett Stream Analytics-jobb som har en strömningsenhet. 

## <a name="reference-data"></a>Referensdata 
Referensdata i ASA läses in i minnet för snabb sökning. Med den aktuella implementeringen behåller varje kopplingsåtgärd med referensdata en kopia av referensdata i minnet, även om du ansluter till samma referensdata flera gånger. För frågor med **PARTITION BY**har varje partition en kopia av referensdata, så partitionerna är helt frikopplade. Med multiplikatoreffekten kan minnesanvändningen snabbt bli mycket hög om du ansluter till referensdata flera gånger med flera partitioner.  

### <a name="use-of-udf-functions"></a>Användning av UDF-funktioner
När du lägger till en UDF-funktion läser Azure Stream Analytics in JavaScript-körningen i minnet. Detta kommer att påverka SU%.

## <a name="next-steps"></a>Nästa steg
* [Skapa parallelliserbara frågor i Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skala Azure Stream Analytics-jobb för att öka dataflödet](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
