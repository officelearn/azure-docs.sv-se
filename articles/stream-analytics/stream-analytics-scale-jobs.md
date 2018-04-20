---
title: Skala uppåt och utåt i Azure Stream Analytics-jobb
description: Den här artikeln beskriver hur du skalar en Stream Analytics-jobbet genom partitionering indata, justera frågan och ange jobbet enheter för strömning.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 1438ffa34652268572fe89dc63583cc25607d722
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Skala Azure Stream Analytics-jobbet för att öka genomströmning
Den här artikeln visar hur du ställer in en Stream Analytics-fråga för att öka genomflödet för Streaming Analytics-jobb. Du kan använda följande guide för att skala ditt jobb för att hantera högre belastning och dra nytta av mer systemresurser (till exempel mer bandbredd, mer CPU-resurser, mer minne).
En förutsättning är kan du behöva läsa följande artiklar:
-   [Förstå och justera direktuppspelningsenheter](stream-analytics-streaming-unit-consumption.md)
-   [Skapa parallell jobb](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Fall 1 – frågan finns i sin natur fullständigt över inkommande partitioner
Om din fråga i sin natur fullständigt mellan inkommande partitioner, kan du följa följande steg:
1.  Redigera frågan ska embarrassingly parallellt med hjälp av **PARTITION BY** nyckelord. Se mer information i avsnittet Embarrassingly parallella jobb [på den här sidan](stream-analytics-parallelization.md).
2.  Beroende på utdata-typer som används i frågan, några utdata antingen inte kanske parallell, eller behöver ytterligare konfiguration för att fungera embarrassingly parallellt. Till exempel är SQL, SQL DW och PowerBI-utdata inte parallell. Utdata är alltid sammanslagna innan du skickar till utdatamottagaren. Automatiskt paralleliserad blobbar, tabeller, ADLS, Service Bus och Azure-funktion. CosmosDB och Event Hub behöver PartitionKey konfigurationen som matchar den **PARTITION BY** fält (vanligtvis PartitionId). För Event Hub också vara extra uppmärksam på att matcha antalet partitioner för alla indata och alla utdata för att undvika över mellan partitioner. 
3.  Kör frågan med **6 SU** (vilket är en nod för databehandling full kapacitet) att mäta högsta möjliga genomströmningen, och om du använder **GROUP BY**, mäta hur många grupper (kardinalitet) jobbet kan hantera. Allmänt problem för jobbet träffa gränserna för system är följande.
    - SU % användning mått är över 80%. Detta anger minne som har hög belastning. De faktorer som bidrar till ökning av det här måttet beskrivs [här](stream-analytics-streaming-unit-consumption.md). 
    -   Produktion tidsstämpel faller med avseende på klocktid. Beroende på din fråga logik kan produktion tidsstämpel ha en förskjutning av logiken från klocktid. De bör dock gå vidare med ungefär samma hastighet. Om produktion tidsstämpel faller ytterligare och ytterligare bakom, är en indikator på att systemet overworking. Det kan vara ett resultat av underordnade utdata sink bandbreddsbegränsning eller hög CPU-användning. Vi tillhandahålla inte CPU-användning mått just nu, så det kan vara svårt att skilja mellan två.
        - Om problemet beror på sink-begränsning kan du behöva öka antalet partitioner för utdata (och även ange partitioner om du vill behålla jobbet fullständigt parallell) eller öka mängden resurser av sink (till exempel antal begäranden för CosmosDB).
    - Jobbet diagram, det finns en per partition eftersläpning händelse mått för varje indata. Om eftersläpning händelse mått fortsätter att öka, men det är också en indikator är så har Systemresursen begränsad (antingen på grund av utdata sink-begränsning eller hög CPU-).
4.  När du har bestämt vad ett 6 SU-jobb kan nå gränser, du kan dra slutsatser linjärt bearbetningskapacitet jobbet när du lägger till flera SUs, förutsatt att du inte har några data skeva som gör vissa partition ”heta”.
>[!Note]
> Välj rätt antal enheter för strömning: eftersom Stream Analytics skapar en nod för bearbetning för varje 6 SU lagts till, det är bäst att göra antalet noder divisor av antalet inkommande partitioner, så att partitionerna kan vara jämnt mellan noder.
> Du har till exempel mäts din 6 SU jobb kan uppnå 4 MB/s bearbetning hastighet och din inkommande partitionsantal är 4. Du kan välja att köra jobbet med 12 SU för att uppnå ungefär 8 MB/s-behandlingstakt eller 24 SU för att uppnå 16 MB/s. Sedan kan du bestämma när ökar antalet SU för att jobbet ska vilket värde som en funktion i vilken takt dina indata.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Case 2 – om din fråga inte embarrassingly parallellt.
Om frågan inte embarrassingly parallella följa du anvisningarna nedan.
1.  Börja med en fråga utan **PARTITION BY** först för att undvika partitionering komplexitet och köra frågan med 6 SU att mäta största belastning som i [fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Om du kan uppnå din förväntade belastningen på sikt genomströmning, är du klar. Du kan också välja att mäta samma jobb som körs på 3 SU och 1 SU för att ta reda på det minsta antalet SU som passar ditt scenario.
3.  Om du inte går att utföra det önskade dataflödet, försök att dela din fråga i flera steg om möjligt om det inte redan har flera steg och allokera upp till 6 SU för varje steg i frågan. Till exempel om du har 3 steg, allokera 18 SU i alternativet ”skala”.
4.  När du kör sådant jobb placerar Stream Analytics varje steg på sin egen nod med dedicerade 6 SU-resurser. 
5.  Om du fortfarande inte har uppnåtts mål-belastning kan du försöker använda **PARTITION BY** från steg närmare till indata. För **GROUP BY** operator som inte kanske naturligt partitionable lokala globala sammanställd mönstret kan använda för att utföra en partitionerad **GROUP BY** följt av en icke-partitionerat **GROUP BY** . Till exempel om du vill räkna kan hur många bilar som passerar varje avgift monter var 3: e minut och mängden data som är större än vad hanteras av 6 SU.

Fråga:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

I frågan ovan räknat bilar per avgift monter per partition och sedan lägga till antalet från alla partitioner tillsammans.

När partitionerad för varje partition för det här steget kan du allokera upp till 6 SU, varje partition med 6 SU är maximalt så att varje partition kan placeras på en egen processnoden.

> [!Note]
> Om din fråga inte får vara partitionerade, kan lägga till ytterligare SU i en fråga med flera steg inte alltid förbättra genomflöde. Ett sätt att få prestanda är att minska volymen på de första stegen lokala globala sammanställd mönster, enligt beskrivningen ovan i steg 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Fall 3 - mängd oberoende frågor körs i ett jobb.
För vissa ISV användningsfall, där det är mer kostnadseffektivt att bearbeta data från flera klienter i ett enda jobb med hjälp av separata indata och utdata för varje klient kan det sluta med en några (till exempel 20) oberoende frågor i ett enda jobb. Antagandet är varje sådan underfråga belastningen är relativt små. I det här fallet kan du följa anvisningarna nedan.
1.  I det här fallet inte använder **PARTITION BY** i frågan
2.  Minska antalet partitioner som indata till det lägsta värdet 2 om du använder Event Hub.
3.  Kör frågan med 6 SU. Förväntade belastningen för varje underfråga lägga till så många underfrågor som möjligt, tills jobbet är träffa gränserna för systemet. Referera till [fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) för symptom när detta sker.
4.  När du träffa underfråga gränsen mäts ovan, kan du börja lägga till underfrågan till ett nytt jobb. Antalet jobb ska köras som en funktion av antal oberoende frågor ska vara ganska linjära, förutsatt att du inte har någon belastningen skeva. Du kan sedan göra prognoser för hur många 6 SU jobb som du behöver köra som en funktion av antalet klienter som du vill hantera.
5.  När du använder referens data koppling med sådana frågor, bör du union indata tillsammans, innan du kan ansluta med samma referensdata sedan dela upp händelser om det behövs. Annars behåller varje referens data anslutning till en kopia av referensdata i minnet, sannolikt blowing in minnesanvändningen i onödan.

> [!Note] 
> Hur många klienter ska ingå i varje jobb?
> Den här frågan mönster ofta har ett stort antal underfrågor och resulterar i mycket stora och komplexa topologi. Styrenhet för jobbet är kanske inte kan hantera en stor topologi. Som en tumregel förblir under 40 innehavare för 1 SU jobbet och 60 klienter för 3 SU och 6 SU-jobb. När du överstiger kapacitet i domänkontrollanten startar inte jobbet har.


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>Ett exempel på Stream Analytics genomflöde i skala
För att hjälpa dig att förstå hur Stream Analytics-jobb skala, utföra vi ett experiment baserat på indata från en hallon Pi-enhet. Experimentet Låt oss se effekten på genomflöde för flera strömmande enheter och partitioner.

I det här scenariot skickar enheten sensordata (klienter) till en händelsehubb. Streaming Analytics bearbetar data och skickar en avisering eller statistik som utdata till en annan event hub. 

Klienten skickar sensordata i JSON-format. Data-utdata är också i JSON-format. Data som ser ut så här:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Följande fråga används för att skicka en avisering när en enstaka stängs av:

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Mäta dataflöde

Dataflödet är mängden inkommande data som bearbetas av Stream Analytics i en fast mängd tid i den här kontexten. (Vi mätt i 10 minuter.) För att uppnå bästa bearbetning-genomströmning för indata, har både inkommande dataström och frågan partitionerats. Vi inkluderat **COUNT()** i frågan för att mäta hur många inkommande händelser har bearbetats. Kontrollera att jobbet inte bara väntade för inkommande händelser komma genom har varje partition för inkommande händelsehubben förinstallerats ungefär 300 GB som indata.

I följande tabell visas de resultat som vi såg när vi har ökat antal enheter för strömning och motsvarande partition räknar i händelsehubbar.  

<table border="1">
<tr><th>Inkommande partitioner</th><th>Utdata partitioner</th><th>Enheter för strömning</th><th>Varaktigt dataflöde
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

Och följande diagram visar en visualisering av relationen mellan SUs och genomflöde.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

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

