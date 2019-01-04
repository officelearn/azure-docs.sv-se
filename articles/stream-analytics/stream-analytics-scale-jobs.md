---
title: Skala uppåt och utåt i Azure Stream Analytics-jobb
description: Den här artikeln beskriver hur du skalar ett Stream Analytics-jobb genom partitionering indata, justera frågan och ställa in jobb enheter för strömning.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: f4307da2e74846507cafb9f767a6ccae855e42a2
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554681"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Skala Azure Stream Analytics-jobb för att öka dataflödet
Den här artikeln visar hur du ställer in en Stream Analytics-fråga för att öka dataflödet för Streaming Analytics-jobb. Du kan använda följande guide för att skala dina jobb för att hantera högre belastning och dra nytta av mer systemresurser (till exempel mer bandbredd, mer CPU-resurser, mer minne).
Som ett krav kan du behöva läsa följande artiklar:
-   [Förstå och justera direktuppspelningsenheter](stream-analytics-streaming-unit-consumption.md)
-   [Skapa kan jobb](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Fall 1 – din fråga är sin natur helt kan över inkommande partitioner
Om din fråga sin natur helt kan mellan inkommande partitioner, kan du följa följande steg:
1.  Skriva din fråga för att vara embarrassingly parallel med hjälp av **PARTITION BY** nyckelord. Se mer information finns i avsnittet Embarrassingly parallel jobb [på den här sidan](stream-analytics-parallelization.md).
2.  Beroende på utdatatyper som används i frågan, några utdata kan antingen inte vara stöder parallellkörning, eller om du behöver ytterligare konfiguration för att bli embarrassingly parallel. Till exempel är SQL, SQL DW och Power BI-utdata inte stöder parallellkörning. Utdata är alltid samman innan du skickar till utdatamottagaren. Blobar, tabeller, ADLS, Service Bus och Azure Function parallelliseras automatiskt. CosmosDB och Händelsehubb måste PartitionKey konfigurationen så att den matchar med den **PARTITION BY** fält (vanligtvis PartitionId). För Event Hub betala extra uppmärksam på matchar antalet partitioner för alla indata- och alla utdata för att undvika över mellan partitioner. 
3.  Kör frågan med **6 SU** (vilket är full kapacitet för en enda databehandling nod) att mäta största möjliga dataflöde, och om du använder **GROUP BY**, mäta hur många grupper (kardinalitet) jobbet kan hantera. Allmän symptomen för jobbet träffa system resursbegränsningar är följande.
    - SU % utnyttjande mått är över 80%. Detta anger minnesanvändningen är hög. Faktorer som påverkar ökning av det här måttet beskrivs [här](stream-analytics-streaming-unit-consumption.md). 
    -   Utdata tidsstämpel faller med avseende på klocktid. Beroende på din frågelogiken kan tidsstämpeln utdata ha en logic-förskjutning från wall-clock-tid. De bör dock löper ungefär desamma. Om utdata tidsstämpel faller ytterligare och ytterligare bakom, är det en indikator på att systemet overworking. Det kan vara ett resultat av underordnade utdata mottagare strypning eller hög CPU-belastning. Vi tillhandahåller inte mått för CPU-användning just nu, så det kan vara svårt att skilja mellan två.
        - Om problemet beror på begränsning av mottagare kan du behöva öka antalet partitioner för utdata (och även ange partitioner för att hålla jobbet helt kan) eller öka mängden resurser komprimeringstyp (till exempel antal enheter för programbegäran för CosmosDB).
    - I jobbdiagrammet finns en per partition eftersläpning händelse mått för varje indata. Om eftersläpning för händelsen mått fortsätter att öka, men det är också en indikator är Systemresursen begränsad (antingen på grund av utdata mottagare av begränsnings- eller hög CPU).
4.  När du har bestämt gränserna för vad ett 6 SU-jobb kan nå, kan du extrapolera linjärt bearbetningskapacitet för jobbet när du lägger till flera SUs, förutsatt att du inte har några data förskjuta som gör vissa partitionen ”heta”.

> [!NOTE]
> Välj rätt antal enheter för strömning: Eftersom Stream Analytics skapar en processnoden för varje 6 SU har lagts till, är det bäst att göra antalet noder som en divisor av antalet inkommande partitioner, så att partitionerna kan fördelas jämnt mellan noderna.
> Exempelvis kan du ha mäts din 6 SU jobbet kan uppnå 4 MB/s pris och dina indata partitionsantal är 4. Du kan välja att köra jobbet med 12 SU att uppnå ungefär 8 MB/s-behandlingstakt eller 24 SU att uppnå 16 MB/s. Sedan kan du bestämma när du vill öka SU antal tills jobbet vilket värde som en funktion av din inkommande frekvens.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Fall 2 – om din fråga inte embarrassingly parallel.
Om din fråga inte embarrassingly parallel, kan du följa följande steg.
1.  Börja med en fråga utan **PARTITION BY** först för att undvika partitionering komplexitet och kör din fråga med 6 SU att mäta maximala belastningen som i [fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Om du kan uppnå den förväntade belastningen på sikt dataflöde, är du klar. Du kan också välja att mäta samma jobb som körs på 3 SU och 1 SU för att ta reda på det minsta antalet SU som passar ditt scenario.
3.  Om du inte går att utföra det önskade dataflödet, försök att dela upp din fråga i flera steg om det är möjligt om den inte redan har flera steg, och tilldela upp till 6 SU för varje steg i frågan. Till exempel om du har 3 steg, allokera 18 SU i alternativet ”skala”.
4.  När du kör sådana jobb, placerar Stream Analytics varje steg på sin egen nod med dedikerade 6 SU-resurser. 
5.  Om du fortfarande inte har uppnått belastningen målet kan du försöker använda **PARTITION BY** börjar från steg närmare indata. För **GROUP BY** operator som inte är naturligt partitionable, lokal/global sammanställd mönstret kan använda för att utföra en partitionerad **GROUP BY** följt av en icke-partitionerad **GROUP BY** . Till exempel om du vill räkna kan hur många bilar som går igenom varje avgift monter var 3: e minut och mängden data som ligger utanför vad hanteras av 6 SU.

Fråga:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
I frågan ovan, du räkna bilar per avgift monter per partition och sedan lägga till antalet från alla partitioner tillsammans.

När partitioneras för varje partition i steget där du allokera upp till 6 SU-och varje partition med 6 SU är maximum, så att varje partition kan placeras på en egen nod för bearbetning.

> [!Note]
> Om din fråga inte får vara partitionerade, kanske att lägga till ytterligare SU i en fråga i flera steg inte alltid förbättra genomflödet. Ett sätt att få prestanda är att minska volymen på de första stegen som använder lokal/global sammanställd mönstret, enligt beskrivningen i steg 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Fall 3 – massor av oberoende frågor körs i ett jobb.
För vissa ISV användarfall, där det är mer kostnadseffektivt att bearbeta data från flera klienter i ett enskilt jobb med hjälp av separata indata och utdata för varje klient kan det sluta köra ganska några (till exempel 20) oberoende frågor i ett enskilt jobb. Det antas att varje sådan underfråga belastningen är relativt små. I det här fallet kan du följa anvisningarna nedan.
1.  Använd i så fall inte **PARTITION BY** i frågan
2.  Minska antalet partitioner som indata till det lägsta värdet 2 om du använder Event Hub.
3.  Kör frågan med 6 SU. Med förväntade belastningen för varje underfråga att lägga till så många underfrågor som möjligt, tills jobbet belastas systemet resursbegränsningar. Referera till [fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) efter problem när det händer.
4.  När du nått gränsen för underfråga som mäts ovan, kan du börja lägga till underfrågan till ett nytt projekt. Antalet jobb som ska köras som en funktion av antalet oberoende frågor ska vara ganska linjär, förutsatt att du inte har all belastning skeva. Du kan sedan beräkna hur många 6 SU-jobb måste du köra som en funktion av hur många klienter som du vill hantera.
5.  När du använder referens data join med sådana frågor, union tillsammans innan du kan ansluta med samma indata refererar till data. Dela sedan bort händelser om det behövs. Annars behåller varje referens data join en kopia av referensdata i minnet, sannolikt blowing upp minnesanvändningen onödan.

> [!Note] 
> Hur många klienter ska placeras i varje jobb?
> Det här mönstret för frågan ofta har ett stort antal underfrågor och resulterar i mycket stora och komplexa topologi. Styrenheten för jobbet är kanske inte kan hantera en stor topologi. Håll under 40 klienter för 1 SU-jobb och 60 klienter som en tumregel för 3 SU och 6 SU-jobb. När du överskrider kapaciteten för kontrollanten, startar inte jobbet har.



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

