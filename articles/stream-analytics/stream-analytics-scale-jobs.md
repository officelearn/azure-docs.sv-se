---
title: Skala upp och ut i Azure Stream Analytics-jobb
description: I den här artikeln beskrivs hur du skalar ett Stream Analytics-jobb genom att partitionera indata, justera frågan och ange jobbströmningsenheter.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: d828103bef8e57f5d0cdfe6c243c52e2d0526663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257554"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Skala ett Azure Stream Analytics-jobb för att öka dataflödet
Den här artikeln visar hur du ställer in en Stream Analytics-fråga för att öka dataflödet för Streaming Analytics-jobb. Du kan använda följande guide för att skala ditt jobb för att hantera högre belastning och dra nytta av fler systemresurser (till exempel mer bandbredd, mer CPU-resurser, mer minne).
Som en förutsättning kan du behöva läsa följande artiklar:
-   [Förstå och justera direktuppspelningsenheter](stream-analytics-streaming-unit-consumption.md)
-   [Skapa parallelliserbara jobb](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Fall 1 – Din fråga är till sin natur helt parallelliserbar över indatapartitioner
Om frågan i sig är helt parallelliserbar över indatapartitioner kan du följa följande steg:
1.  Författare din fråga att vara pinsamt parallell med hjälp av **PARTITION BY** sökord. Se mer information i avsnittet Pinsamt parallella jobb [på den här sidan](stream-analytics-parallelization.md).
2.  Beroende på utdatatyper som används i frågan kan vissa utdata antingen inte vara parallelliserbara eller behöver ytterligare konfiguration vara pinsamt parallell. PowerBI-utdata kan till exempel inte parallelliseras. Utdata slås alltid samman innan utdatamottagaren skickas. Blobbar, Tabeller, ADLS, Service Bus och Azure-funktion parallelliseras automatiskt. SQL- och SQL DW-utgångar har ett alternativ för parallellisering. Event Hub måste ha PartitionKey-konfigurationen inställd för att matcha med **partition by-fältet** (vanligtvis PartitionId). För Event Hub, också ägna extra uppmärksamhet åt att matcha antalet partitioner för alla indata och alla utgångar för att undvika cross-over mellan partitioner. 
3.  Kör frågan med **6 SU** (som är den fulla kapaciteten för en enda datornod) för att mäta maximalt uppnåeligt dataflöde, och om du använder **GROUP BY**mäter du hur många grupper (kardinalitet) som jobbet kan hantera. Allmänna symptom på jobbet som träffar systemresursgränser är följande.
    - SU % utnyttjandemått är över 80%. Detta indikerar att minnesanvändningen är hög. De faktorer som bidrar till ökningen av detta mått beskrivs [här](stream-analytics-streaming-unit-consumption.md). 
    -   Tidsstämpeln för utmatning halkar efter när det gäller väggklockan. Beroende på frågelogiken kan utdatatidsstämpeln ha en logikförskjutning från väggklockatiden. De bör dock utvecklas i ungefär samma takt. Om utdatatidsstämpeln sjunker allt längre efter är det en indikator på att systemet överarbetar. Det kan vara ett resultat av nedströms utdatamottagare begränsning, eller hög CPU-användning. Vi tillhandahåller inte cpu-utnyttjandemått just nu, så det kan vara svårt att skilja de två.
        - Om problemet beror på sink begränsning, kan du behöva öka antalet utdatapartitioner (och även indatapartitioner för att hålla jobbet helt parallelliserbara), eller öka mängden resurser i diskhon (till exempel antalet begärande enheter för CosmosDB).
    - I jobbdiagram finns det ett händelsemått för per partitionseftersläpning för varje indata. Om eftersläpningshändelsemåttet fortsätter att öka är det också en indikator på att systemresursen är begränsad (antingen på grund av utdatamottagaresbegränsning eller hög PROCESSOR).
4.  När du har fastställt gränserna för vad ett 6 SU-jobb kan nå kan du extrapolera manuellt bearbetningskapaciteten för jobbet när du lägger till fler SUs, förutsatt att du inte har några data skeva som gör vissa partition "heta".

> [!NOTE]
> Välj rätt antal strömningsenheter: Eftersom Stream Analytics skapar en bearbetningsnod för varje 6 SU som läggs till är det bäst att göra antalet noder till en divisor av antalet indatapartitioner, så att partitionerna kan fördelas jämnt över noderna.
> Du har till exempel mätt ditt 6 SU-jobb kan uppnå 4 MB/s bearbetningshastighet och antalet indatapartitioner är 4. Du kan välja att köra ditt jobb med 12 SU för att uppnå ungefär 8 MB / s processorhastighet, eller 24 SU för att uppnå 16 MB / s. Du kan sedan bestämma när SU-numret ska ökas för jobbet till vilket värde, som en funktion av inmatningshastigheten.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Fall 2 - Om din fråga inte är pinsamt parallell.
Om frågan inte är pinsamt parallell kan du följa följande steg.
1.  Börja med en fråga utan **PARTITION BY** först för att undvika partitionering komplexitet, och kör din fråga med 6 SU för att mäta maximal belastning som i [fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Om du kan uppnå din förväntade belastning i tid för dataflöde, är du klar. Alternativt kan du välja att mäta samma jobb som körs på 3 SU och 1 SU, för att ta reda på det minsta antalet SU som fungerar för ditt scenario.
3.  Om du inte kan uppnå önskat dataflöde kan du försöka dela upp frågan i flera steg om möjligt, om den inte redan har flera steg och allokera upp till 6 SU för varje steg i frågan. Om du till exempel har 3 steg allokerar du 18 SU i alternativet "Skala".
4.  När stream Analytics kör ett sådant jobb placeras varje steg på sin egen nod med dedikerade 6 SU-resurser. 
5.  Om du fortfarande inte har uppnått ditt belastningsmål kan du försöka använda **PARTITION BY** från stegen närmare indata. För **operatorN GROUP BY** som kanske inte är naturligt partitionerbar kan du använda det lokala/globala samlingsmönstret för att utföra en partitionerad GROUP **BY** följt av en icke-partitionerad **GROUP BY**. Om du till exempel vill räkna hur många bilar som går igenom varje betalbås var tredje minut, och datavolymen är utöver vad som kan hanteras av 6 SU.

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
I frågan ovan räknar du bilar per vägtullmonter per partition och lägger sedan till antalet från alla partitioner tillsammans.

När partitioneras, för varje partition i steget, fördela upp till 6 SU, varje partition med 6 SU är den maximala, så varje partition kan placeras på sin egen bearbetning nod.

> [!Note]
> Om frågan inte kan partitioneras kan det hända att lägga till ytterligare SU i en fråga i flera steg inte alltid förbättrar dataflödet. Ett sätt att få prestanda är att minska volymen på de första stegen med hjälp av lokala / globala aggregerade mönster, som beskrivs ovan i steg 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Fall 3 - Du kör massor av oberoende frågor i ett jobb.
För vissa ISV-användningsfall, där det är mer kostnadseffektivt att bearbeta data från flera klienter i ett enda jobb, med hjälp av separata indata och utdata för varje klient, kan du sluta köra en hel del (till exempel 20) oberoende frågor i ett enda jobb. Antagandet är varje sådan subquery belastning är relativt liten. I det här fallet kan du följa följande steg.
1.  Använd i så fall inte **PARTITION BY** i frågan
2.  Minska antalet indatapartitioner till lägsta möjliga värde på 2 om du använder Event Hub.
3.  Kör frågan med 6 SU. Med förväntad belastning för varje underkö lägger du till så många sådana underfrågor som möjligt tills jobbet når systemresursgränser. Se [fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) för symtomen när detta inträffar.
4.  När du har nått den underköna gräns som mäts ovan börjar du lägga till underfrågan i ett nytt jobb. Antalet jobb som ska köras som en funktion av antalet oberoende frågor bör vara ganska linjärt, förutsatt att du inte har någon belastning skeva. Du kan sedan prognostisera hur många 6 SU-jobb du behöver köra som en funktion av antalet klienter som du vill betjäna.
5.  När du använder referensdata gå med sådana frågor, union indata tillsammans innan du går med samma referensdata. Dela sedan upp händelserna om det behövs. Annars behåller varje referensdatakoppling en kopia av referensdata i minnet, vilket sannolikt blåser upp minnesanvändningen i onödan.

> [!Note] 
> Hur många hyresgäster att sätta i varje jobb?
> Det här frågemönstret har ofta ett stort antal underfrågor och resulterar i mycket stor och komplex topologi. Den registeransvarige för jobbet kanske inte kan hantera en så stor topologi. Som en tumregel, stanna under 40 hyresgäster för 1 SU jobb, och 60 hyresgäster för 3 SU och 6 SU jobb. När du överskrider styrenhetens kapacitet startar inte jobbet.



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

