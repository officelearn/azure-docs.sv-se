---
title: Avvikelseidentifiering i Azure Stream Analytics (förhandsversion)
description: Den här artikeln beskriver hur du använder Azure Stream Analytics och Azure Machine Learning tillsammans för att identifiera avvikelser.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.custom: seodec18
ms.openlocfilehash: 9ea9cc116a13aac2dca9edf8ba86c933310b5198
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479533"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Avvikelseidentifiering i Azure Stream Analytics

Tillgängliga i både i molnet och Azure IoT Edge, Azure Stream Analytics erbjuder inbyggda baserat avvikelseidentifiering funktioner som kan användas för att övervaka två oftast förekommande avvikelser för maskininlärning: temporär och permanent. Med den **AnomalyDetection_SpikeAndDip** och **AnomalyDetection_ChangePoint** funktion, kan du utföra avvikelseidentifiering direkt i ditt Stream Analytics-jobb.

Machine learning-modeller förutsätter en enhetligt provade tidsserie. Om tidsserien inte uniform, kan du infoga ett aggregering steg med ett rullande fönster innan du anropar avvikelseidentifiering.

Machine learning-åtgärder stöder inte säsongsberoende trender eller flera variate samband.

## <a name="model-accuracy-and-performance"></a>Modeller Precision och prestanda

I allmänhet förbättrar modellens Precision med mer data i Hoppande fönster. Data i den angivna skjutfönster behandlas som en del av dess normala värdeintervall för den aktuella tidsperioden. Modellen endast tar hänsyn till Händelsehistorik över Hoppande fönster för att kontrollera om den aktuella händelsen är avvikande. När fönstret glidande flyttas avlägsnas gamla värden från den modellen.

Funktionerna fungerar genom att upprätta en vissa normal baserat på vad de har sett hittills. Extremvärden identifieras genom att jämföra mot den etablerade standarden inom konfidensnivån. Fönstrets storlek ska baseras på de lägsta händelser som krävs för att träna modellen för normala beteende så att när ett fel inträffar får det skulle kunna identifiera den.

Tänk på att modellens svarstiden ökar med historikstorlek eftersom den måste jämföra med ett högre antal senaste händelser. Vi rekommenderar att bara inkludera nödvändigt antal händelser för bättre prestanda.

Luckor i tidsserien kan vara ett resultat av modellen inte ta emot händelser vid vissa tidpunkter i tid. Den här situationen hanteras av Stream Analytics med hjälp av uppräkning. Historikstorlek, samt en varaktighet för samma skjutfönster används för att beräkna Genomsnittshastigheten då förväntas händelser tas emot.

## <a name="spike-and-dip"></a>Topp- och dip

Tillfällig avvikelser i en time series händelseströmmen är känt som toppar och dalar. Toppar och dalar kan övervakas med operatorn Maskininlärningsbaserade [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exempel på topp- och dip-avvikelseidentifiering](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

I samma skjutfönster, om en andra topp är mindre än den första som är den beräknade poängen för mindre topp förmodligen inte betydande tillräckligt jämfört med poäng för den första topp inom konfidensnivån har angetts. Du kan försök minska modellens förtroende på inställningen för att fånga upp sådana avvikelser. Om du börjar få för många aviseringar, kan du använda ett högre konfidensintervall.

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

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

