---
title: Förstå och justera Direktuppspelningsenheter i Azure Stream Analytics
description: Den här artikeln beskrivs inställningen för strömning och andra faktorer som påverkar prestanda i Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: 84f0c000f54852bbab60a53ecb686656ac86b3de
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002662"
---
# <a name="understand-and-adjust-streaming-units"></a>Förstå och justera Direktuppspelningsenheter

Strömningsenheter (su) representerar de beräkningsresurser som allokeras för att köra ett jobb. Ju fler SU:er, desto fler processor- och minnesresurser allokeras för jobbet. Den här kapaciteten kan du fokusera på där frågans logik och sammanfattningar att behöva hantera maskinvara för att köra din Stream Analytics-jobb inom rimlig.

För att minimera svarstiderna vid bearbetningen av dataströmmar utför Azure Stream Analytics-jobb all bearbetning i minnet. Det direktuppspelade jobbet misslyckas när slut på minne. Därför för ett produktionsjobb är det viktigt att övervaka Resursanvändning för en strömningsuppgift och kontrollera att det finns tillräckligt med resurser allokeras så att de jobb som körs 24/7.

SU % utnyttjande mått, som sträcker sig från 0% till 100%, beskriver minnesanvändningen för din arbetsbelastning. Detta mått är vanligtvis mellan 10 och 20% för en strömningsuppgift med minimal fotavtryck. Om SU % utnyttjande är låg och hämta eftersläpande inkommande händelser, kräver arbetsbelastningen troligt mer beräkningsresurser, vilket kräver att du vill öka antalet su: er. Det är bäst att hålla måttet SU nedan 80% med hänsyn till tillfälliga toppar. Microsoft rekommenderar att du ställer in en avisering på 80% utnyttjande SU mått att förhindra resursuttömning. Mer information finns i [Självstudie: Konfigurera aviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurera Stream Analytics Strömningsenheter (su)
1. Logga in på [Azure-portalen](https://portal.azure.com/)

2. Hitta ett Stream Analytics-jobb som du vill skala och öppna den i listan över resurser. 

3. På jobbsidan under den **konfigurera** väljer **skala**. 

    ![Azure Stream Analytics-jobbet Portalkonfiguration][img.stream.analytics.preview.portal.settings.scale]
    
4. Använd skjutreglaget för att ange SUs för jobbet. Observera att du är begränsad till specifika SU-inställningar. 

## <a name="monitor-job-performance"></a>Övervaka jobb prestanda
Du kan spåra dataflödet för ett jobb med Azure portal:

![Azure Stream Analytics övervaka jobb][img.stream.analytics.monitor.job]

Beräkna det förväntade dataflödet av arbetsbelastningen. Om dataflödet är mindre än förväntat, finjustera indatapartitionen, justera frågan och lägga till su: er för dina jobb.

## <a name="how-many-sus-are-required-for-a-job"></a>Hur många su: er som krävs för ett jobb?

Välja antalet nödvändiga su: er för ett visst jobb beror på partitionskonfigurationen för indata och frågan som definierats i jobbet. Den **skala** sidan kan du ange rätt antal su: er. Det är en bra idé att allokera mer SUs än vad som behövs. Motor för Stream Analytics händelsebearbetning optimeras för svarstid och dataflöde men kräver ytterligare minnesallokering.

I allmänhet är det bästa sättet är att starta med 6 SUs för frågor som inte använder **PARTITION BY**. Sedan fastställa söta platsen genom att använda en prövningsmetod med där du ändra hur många su: er när du skickar representativa mängder data och undersöka måttet SU % utnyttjande. Det maximala antalet enheter för strömning som kan användas av ett Stream Analytics-jobb beror på hur många av stegen i frågan som definierats för jobbet och antalet partitioner i varje steg. Du kan lära dig mer om gränserna [här](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Mer information om hur du väljer rätt antal su: er finns i den här sidan: [Skala Azure Stream Analytics-jobb för att öka dataflödet](stream-analytics-scale-jobs.md)

> [!Note]
> Välja hur många su: er som krävs för ett specifikt jobb beror på partitionskonfigurationen för indata och frågan som definierats för jobbet. Du kan välja upp till din kvot i su: er för ett jobb. Som standard har en kvot på upp till 200 SUs för analytics-jobb i en viss region i varje Azure-prenumeration. För att öka su: er för dina prenumerationer utöver den här kvoten kan kontakta [Microsoft Support](https://support.microsoft.com). Giltiga värden för su: er per jobb är 1, 3, 6, och upp i steg 6.

## <a name="factors-that-increase-su-utilization"></a>Faktorer som ökar SU % utnyttjande 

Temporala (time-orienterade) fråga element är den grundläggande uppsättningen tillståndskänsliga operatörer som tillhandahålls av Stream Analytics. Stream Analytics hanterar tillståndet för de här åtgärderna internt för användarens räkning genom att hantera minnesförbrukning, kontrollpunkter för återhämtning och återställning av systemtillstånd under uppgraderingar av tjänsten. Även om Stream Analytics hanterar fullständigt tillstånd, finns det ett antal rekommendationer om metodtips som användare bör tänka på.

Observera att ett jobb med komplex fråga logic kan ha hög SU % utnyttjande även när den inte tar emot inkommande händelser kontinuerligt. Detta kan inträffa efter en plötslig insamling i indata- och händelser. Jobbet kan fortsätta att upprätthålla tillståndet i minnet om frågan är komplex.

## <a name="stateful-query-logicin-temporal-elements"></a>Tillståndskänsliga frågans logik i temporala element
En av den unika funktionen för Azure Stream Analytics-jobb är att utföra tillståndskänsliga bearbetning, till exempel fönsteraggregeringar, temporala kopplingar och temporala analysfunktioner. Var och en av de här operatorerna behåller statusinformation. Den maximala fönsterstorleken för de här elementen i frågan är sju dagar. 

Begreppet temporalt fönster visas i flera element i Stream Analytics-fråga:
1. Fönsteraggregeringar: GRUPP av av rullande, hoppar och glidande windows

2. Den temporala kopplingar: TRÄFFA DATEDIFF-funktionen

3. Den temporala analysfunktioner: ISFIRST-, LAST och FÖRDRÖJNING med LIMIT DURATION

Följande faktorer påverkar det minne som används (del av strömmande enheter mått) av Stream Analytics-jobb:

## <a name="windowed-aggregates"></a>Mängdfunktioner med fönster
Det minne som förbrukas (tillstånd storleken) för en Windows-mängd är inte alltid direkt proportion till storleken på. Förbrukat minne är istället proportion till Kardinaliteten för data eller antalet grupper i varje tidsfönster.


Till exempel i följande fråga, hur många som är associerade med `clusterid` är frågans kardinalitet. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

För att åtgärda problem som orsakas av hög kardinalitet i den föregående frågan kan du skicka händelser till Event Hub partitioneras efter `clusterid`, och skala ut frågan genom att låta systemet för att bearbeta varje indatapartitionen separat enligt våra **PARTITION GENOM att** som visas i exemplet nedan:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

När frågan är utpartitionerad sprids den ut över flera noder. Resultatet blir att, antalet `clusterid` värden som kommer till varje nod, vilket minskar kardinalitet i gruppen av operatör. 

Event Hub-partitioner bör partitioneras med gruppering för att undvika behovet av ett reduceringssteg. Mer information finns i [översikt av Händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Den temporala kopplingar
Det minne som förbrukas en temporal koppling (tillstånd storlek) är proportionell mot antalet händelser i temporala handlingsfrihet för kopplingen som är inkommande takten multiplicera genom att skaka några gånger... rummet storlek. Med andra ord att det minne som förbrukas av kopplingar är proportionell mot DateDiff tidsintervallet multiplicerat med genomsnittlig händelsefrekvens.

Antalet omatchade händelser i kopplingen påverkar minnesanvändning för frågan. Följande fråga söker efter reklamannonser som genererar klick:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

I det här exemplet är det möjligt att många annonser visas och några användare klickar på den och det krävs för att behålla alla händelser i tidsfönstret. Förbrukat minne beror på tidsperiodens längd och händelsens frekvens. 

Åtgärda det genom att skicka händelser till Event Hub partitioneras efter anslutning till nycklar (id i det här fallet) och skala ut frågan genom att låta systemet att bearbeta varje indatapartitionen separat enligt våra **PARTITION BY** enligt:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

När frågan är utpartitionerad sprids den ut över flera noder. Därmed, antalet händelser som kommer till varje nod vilket minskar storleken på tillståndet sparas i fönstret join. 

## <a name="temporal-analytic-functions"></a>Den temporala analysfunktioner
Det minne som förbrukas en temporal analysfunktionen (tillstånd storlek) är proportionell mot takten multiplicering av varaktigheten. Det minne som förbrukas av analysfunktioner står inte i proportion till storleken på, men i stället partitionera antalet i varje tidsfönster.

Reparationen liknar temporala koppling. Du kan skala ut en fråga med hjälp av **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Oordnade bufferten 
Användaren kan konfigurera den oordnade buffertstorleken i den händelse att sorteringen konfigurationsruta. Bufferten används för att lagra indata för varaktighet för perioden och ändra ordning på dem. Storleken på bufferten är proportionell mot den inkommande takten multiplicering av oordnade fönsterstorlek. Fönstret standardstorleken är 0. 

Om du vill åtgärda spill oordnade buffertens, skala ut frågan med **PARTITION BY**. När frågan är utpartitionerad sprids den ut över flera noder. Därför kan, antalet händelser som kommer till varje nod vilket minskar antalet händelser i varje ordna om buffert. 

## <a name="input-partition-count"></a>Antal inkommande partitioner 
Varje indatapartitionen för ett jobb som indata har en buffert. Större antalet inkommande partitioner, desto mer resurser förbrukar jobbet. För varje enhet för strömning, kan Azure Stream Analytics bearbeta ungefär 1 MB/s av indata. Därför kan du optimera genom att matcha antalet Stream Analytics strömningsenheter med antalet partitioner i Event Hub. 

Ett jobb som konfigurerats med en enhet för strömning är vanligtvis tillräckligt för en Händelsehubb med två partitioner (som är minimum för Event Hub). Om Event Hub har fler partitioner, ditt Stream Analytics-jobb förbrukar mer resurser, men inte nödvändigtvis använder extra dataflödet som tillhandahålls av Event Hub. 

För ett jobb med 6 enheter för strömning, kanske du behöver 4 eller 8 partitioner från Händelsehubben. Undvik dock att för många onödiga partitioner eftersom som leder till orimlig Resursanvändning. Till exempel en Händelsehubb med 16 partitioner eller större i ett Stream Analytics-jobb som har 1 enhet för strömning. 

## <a name="reference-data"></a>Referensdata 
Referensdata i ASA läses in i minnet för snabb sökning. Med den aktuella implementationen behålls varje join-åtgärd med referensdata ett exemplar av referensdata i minnet, även om du blir medlem med samma referensdata flera gånger. För frågor med **PARTITION BY**, varje partition har en kopia av referensdata, så att partitionerna är helt fristående. Med effekten multiplikatorn få minnesanvändning snabbt mycket hög om du går med med referensdata flera gånger med flera partitioner.  

### <a name="use-of-udf-functions"></a>Användning av UDF-funktioner
När du lägger till en UDF-funktion läser Azure Stream Analytics JavaScript-körning i minnet. Detta påverkar SU %.

## <a name="next-steps"></a>Nästa steg
* [Skapa kan frågor i Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skala Azure Stream Analytics-jobb för att öka dataflödet](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
