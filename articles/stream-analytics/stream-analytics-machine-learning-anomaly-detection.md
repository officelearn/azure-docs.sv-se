---
title: "Avvikelseidentifiering i Azure användning Guide (förhandsversion) | Microsoft Docs"
description: "Använd stream analytics och maskininlärning att identifiera avvikelser."
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: dubansal
ms.openlocfilehash: db72b1ca936e69a049d64f939d3399bfd9cdf89c
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="using-the-anomalydetection-operator"></a>Med hjälp av operatorn ANOMALYDETECTION

> [!IMPORTANT]
> Den här funktionen är i förhandsgranskningen. Vi rekommenderar inte används i produktionen.

Den **ANOMALYDETECTION** operatorn kan användas för att identifiera avvikelser i händelseströmmar.
Till exempel en långsam minskning av ledigt minne under lång tid kan tyda på en minnesläcka eller antalet webbtjänstbegäranden som är stabil inom ett intervall kan kraftigt öka eller minska.

Söker efter följande typer av avvikelser över den angivna varaktigheten:

- Dubbelriktad förändring
- Långsam positivt trend
- Långsam negativt trend

Tidsintervallet för hur långt tillbaka i historiken från den aktuella händelsen måste ha i åtanke begränsas med hjälp av den **LIMIT DURATION** satsen. **ANOMALYDETECTION** kan eventuellt begränsas till enbart de händelser som matchar en viss egenskap eller villkor med hjälp av den **när** satsen.

Det kan också bearbeta händelsegrupper separat baserat på nyckeln som anges i den **PARTITION BY** satsen. Utbildning och förutsägelse uppstå oberoende i varje partition.

## <a name="syntax"></a>Syntax

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>Exempel på användning

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`|


## <a name="arguments"></a>Argument

- **scalar_expression**

  Det skalära uttrycket som utförs av avvikelseidentifiering. Det är ett uttryck av typen för flyttal eller bigint som returnerar ett enda () skalärvärde. Jokerteckenuttryck  **\***  tillåts inte. **scalar_expression** får inte innehålla andra analysfunktioner eller externa funktioner.

- **ÖVER ([partition_by_clause] limit_duration_clause [when_clause])**

- **partition_by_clause** 

  Den `PARTITION BY <partition key>` satsen dividerar learning och utbildning över olika partitioner. Med andra ord en separat modell används per värde för `<partition key>` och endast händelser som har värdet används för inlärning och utbildning i den här modellen. Exempel:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  kommer träna och betygsätta en läsning mot andra värden för samma sensorn.

- **satsen limit_duration** varaktighet (\<enhet\>, \<längd\>)

  Anger hur mycket av tidigare från den aktuella händelsen anses ingå i den **ANOMALYDETECTION** beräkning. Se DATEDIFF för en detaljerad beskrivning av enheter som stöds och deras förkortningar.

- **when_clause** 

  Anger ett booleskt villkor för händelserna som anses vara i den **ANOMALYDETECTION** beräkning.

## <a name="return-types"></a>Returtyper

Funktionen returnerar en post som innehåller samtliga tre värden som utdata. Egenskaper som är associerade med de olika typerna av avvikelseidentifiering detektorerna kallas:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Extrahera enskilda värden utanför posten med hjälp av **GetRecordPropertyValue** funktion. Exempel:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 


Ett fel av en viss typ har identifierats när något av dessa avvikelseidentifiering poäng överskrider ett tröskelvärde. Tröskelvärdet kan vara en flytande peka nummer \>= 0. Tröskelvärdet är en kompromiss mellan känslighet och förtroende. Ett lägre tröskelvärde skulle göra identifiering känsliga för ändringar och flera aviseringar genereras ett högre tröskelvärde kan göra identifiering mindre känsliga och lita mer men maskera vissa avvikelser. Det exakta tröskelvärdet som ska använda beror på scenariot. Det finns ingen övre gräns, men det rekommenderade intervallet är 3,25 5.

## <a name="algorithm"></a>Algoritmen

**ANOMALYDETECTION** använder glidande fönstret semantik, vilket innebär att beräkningen kör per händelse som anger funktionen och en poäng skapas för den händelsen. Beräkningen baseras på utbyte Martingales som fungerar genom att kontrollera om distribution av händelsevärden har ändrats. I så fall, har potentiella avvikelseidentifiering upptäckts. Det returnerade resultatet är en indikation på konfidensnivån för den avvikelseidentifiering. Som en intern optimering **ANOMALYDETECTION** beräknar avvikelseidentifiering poängen för en händelse baserat på *d* till *2d* händelser, där *d*är storleken på angivna identifiering.

**ANOMALYDETECTION** förväntar sig indata tidsserien vara enhetligt. En händelseström kan göras enhetlig genom att sammanställa över en rullande eller hopping fönster. I scenarier där mellanrummet mellan händelser alltid är mindre än fönstret aggregering, är en rullande fönster tillräcklig för att tidsserien lika. När mellanrummet kan vara större, kan du fylla i luckorna genom att upprepa det sista värdet i ett Hoppande fönster. Båda dessa scenarier kan hanteras av följande exempel. För närvarande den `FillInMissingValuesStep` steg kan hoppas över. Inte med det här steget resulterar i ett kompileringsfel.

## <a name="performance-guidance"></a>Prestandaråd

- Använd 6 SU för jobb. 
- Skicka händelser minst 1 sekund från varandra.
- En icke-partitionerat frågan med hjälp av den **ANOMALYDETECTION** funktion kan ge resultat med en beräkning fördröjning på cirka 25 MS i genomsnitt.
- Svarstiden upplever en partitionerad fråga varierar något med antalet partitioner, som antalet beräkningar är högre. Svarstiden är dock ungefär som partitionerade fallet för en jämförbar Totalt antal händelser för alla partitioner.
- Vid läsning av data i real time, har en stor mängd data inhämtas snabbt. Bearbetning av dessa data är för närvarande avsevärt långsammare. Fördröjning i sådana situationer hittades för att öka med antalet datapunkter i fönstret i stället för fönstret storlek eller händelse intervallet linjärt sig självt. Överväg att använda en mindre fönsterstorlek för att minska fördröjningen i real time fall. Överväg också att starta jobbet från den aktuella tiden. Några exempel på fördröjningar i en icke-partitionerat fråga: 
    - 60 datapunkter i fönstret identifiering kan resultera i en fördröjning på 10 sekunder med en genomströmning på 3 Mbit/s. 
    - Fördröjningen kan nå ungefär 80 sekunder med en genomströmning på 0,4 Mbit/s på 600 datapunkter.

## <a name="example"></a>Exempel

Följande fråga kan användas för att skicka en avisering om ett fel upptäcks.
När Indataströmmen inte uniform hjälper steget aggregering omvandla det till en enhetlig tidsserier. I exemplet används **AVG** men den speciella typ av aggregering beroende på Användarscenario. Dessutom, när en tidsserie har luckor som är större än fönstret aggregering, det blir inga händelser i tidsserien till utlösaren avvikelseidentifiering (enligt glidande fönstret semantik). Därför bryts antagandet om enhetlighet när nästa händelse tas emot. I så fall behöver vi ett sätt att fylla i luckorna i tidsserien. En möjlig metod är att ta den sista händelsen i alla hopp fönster som visas nedan.

Som nämnts innan Hoppa inte över den `FillInMissingValuesStep` steg för tillfället. Om du utesluter steget resulterar i ett kompileringsfel.

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25

## <a name="references"></a>Referenser

* [Avvikelseidentifiering – använder datorn Lär dig att identifiera avvikelser i tid Series-Data](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Maskininlärning Avvikelseidentifiering API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Tid serien Avvikelseidentifiering](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>Få support
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

