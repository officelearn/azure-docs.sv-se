---
title: Avvikelseidentifiering i Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder Azure Stream Analytics och Azure Machine Learning tillsammans för att identifiera avvikelser.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525540"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Avvikelseidentifiering i Azure Stream Analytics

Azure Stream Analytics är tillgängligt i både molnet och Azure IoT Edge och erbjuder inbyggda funktioner för identifiering av avvikelser för maskininlärning som kan användas för att övervaka de två vanligaste avvikelserna: tillfälliga och beständiga. Med **AnomalyDetection_SpikeAndDip** och **AnomalyDetection_ChangePoint** funktioner kan du utföra avvikelseidentifiering direkt i ditt Stream Analytics-jobb.

Maskininlärningsmodellerna antar en enhetligt samplad tidsserie. Om tidsserien inte är enhetlig kan du infoga ett aggregeringssteg med ett tumlande fönster innan du anropar avvikelseidentifiering.

Maskininlärningsåtgärderna stöder inte säsongstrender eller multivariatkorrelationer just nu.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Avvikelseidentifiering med hjälp av maskininlärning i Azure Stream Analytics

Följande video visar hur du identifierar en avvikelse i realtid med hjälp av maskininlärningsfunktioner i Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Modellbeteende

I allmänhet förbättras modellens noggrannhet med mer data i skjutfönstret. Data i det angivna skjutfönstret behandlas som en del av dess normala värdeintervall för den tidsperioden. Modellen tar bara hänsyn till händelsehistorik över skjutfönstret för att kontrollera om den aktuella händelsen är avvikande. När skjutfönstret rör sig vräks gamla värden från modellens träning.

Funktionerna fungerar genom att fastställa en viss normal baserat på vad de har sett hittills. Extremvärden identifieras genom att jämföra med det fastställda normala, inom konfidensnivån. Fönsterstorleken bör baseras på de minsta händelser som krävs för att träna modellen för normalt beteende så att när en anomali inträffar, skulle den kunna känna igen den.

Modellens svarstid ökar med historikstorlek eftersom den måste jämföras med ett större antal tidigare händelser. Det rekommenderas att endast inkludera det nödvändiga antalet händelser för bättre prestanda.

Luckor i tidsserien kan vara ett resultat av att modellen inte tar emot händelser vid vissa tidpunkter. Den här situationen hanteras av Stream Analytics med hjälp av imputeringslogik. Historikstorleken, liksom en tidslängd, för samma skjutfönster används för att beräkna den genomsnittliga hastighet med vilken händelser förväntas komma fram.

En avvikelsegenerator som finns [här](https://aka.ms/asaanomalygenerator) kan användas för att mata en Iot Hub med data med olika avvikelsemönster. Ett ASA-jobb kan ställas in med dessa avvikelseidentifieringsfunktioner för att läsa från den här Iot Hub och identifiera avvikelser.

## <a name="spike-and-dip"></a>Spike och dip

Tillfälliga avvikelser i en tidsseriehändelseström kallas spikar och dips. Spikar och dips kan övervakas med den Machine Learning-baserade [operatören, AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exempel på spike och dip anomali](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

I samma skjutfönster, om en andra spik är mindre än den första, är den beräknade poängen för den mindre spiken förmodligen inte tillräckligt betydande jämfört med poängen för den första spiken inom den angivna konfidensnivån. Du kan försöka minska modellens konfidensnivå för att upptäcka sådana avvikelser. Men om du börjar få för många aviseringar kan du använda ett högre konfidensintervall.

Följande exempelfråga förutsätter en enhetlig inmatningshastighet för en händelse per sekund i ett 2-minuters skjutfönster med en historik över 120 händelser. Den slutliga SELECT-satsen extraherar och matar ut poäng- och avvikelsestatus med en konfidensnivå på 95 %.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Ändringspunkt

Beständiga avvikelser i en tidsseriehändelseström är ändringar i fördelningen av värden i händelseströmmen, till exempel nivåändringar och trender. I Stream Analytics identifieras sådana avvikelser med hjälp av machine learning-operatorn [AnomalyDetection_ChangePoint.](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics)

Ihållande förändringar varar mycket längre än toppar och dips och kan tyda på katastrofala händelser. Beständiga förändringar är vanligtvis inte synliga för blotta ögat, men kan detekteras med **AnomalyDetection_ChangePoint** operatören.

Följande bild är ett exempel på en nivåändring:

![Exempel på nivåändringsavvikelse](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Följande bild är ett exempel på en trendförändring:

![Exempel på avvikelser i trendförändring](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Följande exempelfråga förutsätter en enhetlig inmatningshastighet för en händelse per sekund i ett 20-minuters skjutfönster med en historikstorlek på 1 200 händelser. Den slutliga SELECT-satsen extraherar och matar ut poäng- och avvikelsestatus med en konfidensnivå på 80 %.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Prestandaegenskaper

Prestanda för dessa modeller beror på historikstorlek, fönstervaraktighet, händelsebelastning och om partitionering på funktionsnivå används. I det här avsnittet beskrivs dessa konfigurationer och exempel på hur du kan upprätthålla inmatningshastigheter på 1K-, 5K- och 10 000-händelser per sekund.

* **Historikstorlek** - Dessa modeller presterar linjärt med **historikstorlek**. Ju längre historikstorlek, desto längre tar modellerna för att få en ny händelse. Detta beror på att modellerna jämför den nya händelsen med var och en av de tidigare händelserna i historikbufferten.
* **Fönsterlängd** - **Fönstrets varaktighet** ska återspegla hur lång tid det tar att ta emot så många händelser som anges av historikstorleken. Utan så många händelser i fönstret skulle Azure Stream Analytics tillskriva saknade värden. Därför är CPU-förbrukning en funktion av historikstorleken.
* **Händelsebelastning** - Ju större **händelsebelastning**, desto mer arbete som utförs av modellerna, vilket påverkar CPU-förbrukningen. Jobbet kan skalas ut genom att göra det pinsamt parallellt, förutsatt att det är vettigt för affärslogik att använda fler indatapartitioner.
* **Partitionering** - av**funktionsnivå Funktionsnivå partitionering** görs med hjälp ```PARTITION BY``` av hjälp i anropet för avvikelseidentifiering. Den här typen av partitionering lägger till en overhead, eftersom tillståndet måste underhållas för flera modeller samtidigt. Partitionering på funktionsnivå används i scenarier som partitionering på enhetsnivå.

### <a name="relationship"></a>Relation
Historikstorlek, fönsterlängd och total händelsebelastning är relaterade på följande sätt:

fönsterVarnare (i ms) = 1000 * historikStorlek / (Totalt antal indatahändelser per sek/ antal indatapartitioner)

När du partitionerar funktionen efter deviceId lägger du till "PARTITION BY deviceId" i anropet för avvikelseidentifiering.

### <a name="observations"></a>Observationer
Följande tabell innehåller dataflödesobservationer för en enda nod (6 SU) för det icke-partitionerade skiftet:

| Historikstorlek (händelser) | Fönsterlängd (ms) | Totalt antal indatahändelser per sekund |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2200 |
| 600 | 728 | 1,650 |
| 6 000 | 10,910 | 1 100 |

Följande tabell innehåller dataflödesobservationer för en enda nod (6 SU) för det partitionerade skiftet:

| Historikstorlek (händelser) | Fönsterlängd (ms) | Totalt antal indatahändelser per sekund | Antal enheter |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1 100 | 10 |
| 600 | 10,910 | 1 100 | 10 |
| 6 000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6 000 | 2,181,819 | <550 | 100 |

Exempelkod för att köra de icke-partitionerade konfigurationerna ovan finns i [delningsbaserad delning](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) av Azure-exempel. Koden skapar ett dataflödesanalysjobb utan partitionering på funktionsnivå, som använder Event Hub som indata och utdata. Indatabelastningen genereras med hjälp av testklienter. Varje indatahändelse är ett 1KB json-dokument. Händelser simulerar en IoT-enhet som skickar JSON-data (för upp till 1K-enheter). Historikstorlek, fönsterlängd och total händelsebelastning varieras över 2 indatapartitioner.

> [!Note]
> Om du vill ha en mer exakt uppskattning anpassar du exemplen så att de passar ditt scenario.

### <a name="identifying-bottlenecks"></a>Identifiera flaskhalsar
Använd fönstret Mått i ditt Azure Stream Analytics-jobb för att identifiera flaskhalsar i pipelinen. Granska **indata-/utdatahändelser** för dataflöde och ["Watermark Delay"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) eller **Backlogged Events** för att se om jobbet håller jämna steg med indatahastigheten. Leta efter **begränsade begäranden** för händelsehubbar och justera tröskelvärdena i enlighet med detta. För Cosmos DB-mått bör du granska **Max förbrukade RU/s per partitionsnyckelintervall** under Dataflöde för att säkerställa att dina partitionsnyckelintervall förbrukas på ett enhetligt sätt. För Azure SQL DB övervakar du **Log IO** och **CPU**.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

