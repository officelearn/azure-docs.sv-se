---
title: Avvikelseidentifiering i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder Azure Stream Analytics och Azure Machine Learning tillsammans för att identifiera avvikelser.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612344"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Avvikelseidentifiering i Azure Stream Analytics

Tillgängliga i både i molnet och Azure IoT Edge, Azure Stream Analytics erbjuder inbyggda baserat avvikelseidentifiering funktioner som kan användas för att övervaka två oftast förekommande avvikelser för maskininlärning: temporär och permanent. Med den **AnomalyDetection_SpikeAndDip** och **AnomalyDetection_ChangePoint** funktion, kan du utföra avvikelseidentifiering direkt i ditt Stream Analytics-jobb.

Machine learning-modeller förutsätter en enhetligt provade tidsserie. Om tidsserien inte uniform, kan du infoga ett aggregering steg med ett rullande fönster innan du anropar avvikelseidentifiering.

Machine learning-åtgärder stöder inte säsongsberoende trender eller flera variate korrelationer just nu.

## <a name="model-behavior"></a>Beteendet för enhetsmodellen

I allmänhet förbättrar modellens Precision med mer data i Hoppande fönster. Data i den angivna skjutfönster behandlas som en del av dess normala värdeintervall för den aktuella tidsperioden. Modellen endast tar hänsyn till Händelsehistorik över Hoppande fönster för att kontrollera om den aktuella händelsen är avvikande. När fönstret glidande flyttas avlägsnas gamla värden från den modellen.

Funktionerna fungerar genom att upprätta en vissa normal baserat på vad de har sett hittills. Extremvärden identifieras genom att jämföra mot den etablerade standarden inom konfidensnivån. Fönstrets storlek ska baseras på de lägsta händelser som krävs för att träna modellen för normala beteende så att när ett fel inträffar får det skulle kunna identifiera den.

Modellens svarstiden ökar med historikstorlek eftersom den måste jämföra med ett högre antal senaste händelser. Vi rekommenderar att bara inkludera nödvändigt antal händelser för bättre prestanda.

Luckor i tidsserien kan vara ett resultat av modellen inte ta emot händelser vid vissa tidpunkter i tid. Den här situationen hanteras av Stream Analytics med hjälp av uppräkning logik. Historikstorlek, samt en varaktighet för samma skjutfönster används för att beräkna Genomsnittshastigheten då förväntas händelser tas emot.

En tillgänglig avvikelseidentifiering generator [här](https://aka.ms/asaanomalygenerator) kan användas för att mata in en Iot-hubb med data med olika avvikelseidentifiering mönster. ASA-jobb kan konfigureras med dessa funktioner för identifiering av avvikelser att läsa från den här Iot-hubben och identifiera avvikelser.

## <a name="spike-and-dip"></a>Topp- och dip

Tillfällig avvikelser i en time series händelseströmmen är känt som toppar och dalar. Toppar och dalar kan övervakas med operatorn Maskininlärningsbaserade [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exempel på topp- och dip-avvikelseidentifiering](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

I samma skjutfönster, om en andra topp är mindre än den första som är den beräknade poängen för mindre topp förmodligen inte betydande tillräckligt jämfört med poäng för den första topp inom konfidensnivån har angetts. Du kan försök minska modellens konfidensnivån för att identifiera sådana avvikelser. Om du börjar få för många aviseringar, kan du använda ett högre konfidensintervall.

Följande exempelfråga förutsätter en enhetlig inkommande sats för en händelse per sekund i en glidande femminutersperiod i 2 med en historik över händelser på 120. Sista SELECT-instruktionen extraherar och visar blobens poäng och avvikelseidentifiering status med en konfidensnivå på 95%.

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

## <a name="change-point"></a>Ändra återställningspunkt

Beständiga avvikelser i en time series händelseströmmen är ändringar i distributionen av värden i händelseströmmen, som ändras och trender. I Stream Analytics sådana avvikelserna identifieras med hjälp av den Maskininlärningsbaserade [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) operator.

Permanenta ändringar räcker mycket längre än toppar och dalar och kan tyda på kritiska händelser. Permanenta ändringar visas inte vanligtvis för blotta ögat, men kan identifieras med den **AnomalyDetection_ChangePoint** operator.

Följande bild är ett exempel på en nivå ändring:

![Exempel på nivåändring avvikelseidentifiering](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Följande bild är ett exempel på en trend ändring:

![Exempel på trend ändra avvikelseidentifiering](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Följande exempelfråga förutsätter en enhetlig inkommande sats för en händelse per sekund i ett skjutfönster på 20 minuter med en historikstorlek på 1200-händelser. Sista SELECT-instruktionen extraherar och visar blobens poäng och avvikelseidentifiering status med en konfidensnivå på 80%.

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

Prestanda för dessa modeller beror på tidigare storlek, fönstervaraktigheten, händelsebelastningen, och om funktionen på partitionering används. Det här avsnittet beskriver de här konfigurationerna och innehåller exempel att kunna fortsätta arbeta enligt datainmatningsfrekvensen 1K, 5K och 10K händelser per sekund.

* **Historikstorlek** -dessa modeller utföra linjärt med **historikstorlek**. Historikstorlek, desto längre tid tar ju längre modellerna kan bedöma de en ny händelse. Det beror på att modellerna jämför den nya händelsen till var och en av tidigare händelser i bufferten historik.
* **Fönstervaraktigheten** – **fönstervaraktigheten** bör återspegla hur lång tid det tar att ta emot så många händelser som den anges av den tidigare storleken. Azure Stream Analytics skulle utan så många händelser i fönstret sedan imputera värden som saknas. CPU-förbrukning är därför en funktion av samma historikstorlek som.
* **Händelsebelastningen** – ju större den **händelsebelastningen**, desto mer arbete som utförs av modeller, vilket påverkar CPU-förbrukning. Jobbet kan skaländras ut genom att göra det embarrassingly parallel, förutsatt att det passar för affärslogik för att använda mer inkommande partitioner.
* **Funktionen på partitionering** - **funktion på partitionering** görs med hjälp av ```PARTITION BY``` i funktionsanropet för identifiering av avvikelser. Den här typen av partitionering lägger till en belastning som tillstånd måste underhållas för flera modeller på samma gång. Funktionen på partitionering används i scenarier som enheten på partitionering.

### <a name="relationship"></a>Relation
Historikstorlek, fönstervaraktigheten och totala händelsebelastningen relaterade på följande sätt:

windowDuration (i ms) = 1000 * historySize / (totalt antal indata händelser Per sekund / antalet partitioner för indata)

Lägg till ”PARTITION av deviceId” till funktionsanropet avvikelseidentifiering identifiering när partitionering funktionen av deviceId.

### <a name="observations"></a>Observationer
Följande tabell innehåller dataflöde observationer för en enskild nod (6 SU) för icke-partitionerad:

| Historikstorlek (händelser) | Fönstret varaktighet (ms) | Totalt antal inkommande händelser per sekund |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

Följande tabell innehåller dataflöde observationer för en enskild nod (6 SU) för det partitionerade ärendet:

| Historikstorlek (händelser) | Fönstret varaktighet (ms) | Totalt antal inkommande händelser per sekund | Enhetsantal |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

Exempelkod för att köra de icke-partitionerad konfigurationerna som angetts ovan finns i den [direktuppspelning i skala lagringsplatsen](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) av Azure-exempel. Koden skapar ett stream analytics-jobb med ingen funktion på partitionering, som använder Event Hub som indata och utdata. Den inkommande belastningen genereras med hjälp av testklienter. Varje händelse är ett json-dokument på 1KB. Händelser simulera en IoT-enhet som skickar JSON-data (för upp till 1 K-enheter). Historikstorlek, fönstervaraktigheten och totala händelsebelastningen varieras under 2 inkommande partitioner.

> [!Note]
> Anpassa exemplen för att passa ditt scenario för en mer tillförlitlig uppskattning.

### <a name="identifying-bottlenecks"></a>Identifiera flaskhalsar
Använda fönstret mått i Azure Stream Analytics-jobb för att identifiera flaskhalsar i din pipeline. Granska **indata/utdata-händelser** för dataflöde och [”vattenstämpel fördröjning”](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) eller **eftersläpande händelser** att se om jobbet är att hålla igång med indata. För Event Hub-mått, leta efter **begränsade begäranden** och justera tröskelvärdet enheter därefter. Cosmos DB-mått, granska **Max konsumerade RU/s per partitionsnyckelintervall** under dataflöde för att se till att partitionen nyckelintervall används ett enhetligt sätt. Azure SQL DB, övervaka **logg-IO** och **CPU**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Avvikelseidentifiering med machine learning i Azure Stream Analytics

Följande videoklipp visar hur du kan identifiera avvikelser i realtid med machine learning-funktioner i Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

