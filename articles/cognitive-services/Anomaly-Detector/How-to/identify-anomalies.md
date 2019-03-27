---
title: 'Hur du använder API: T för Avvikelseidentifiering detektor på time series-data'
description: Lär dig att identifiera avvikelser i dina data som en batch eller på strömmande data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473335"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Anvisningar: Använda API: T för Avvikelseidentifiering detektor på time series-data  

Den [Avvikelseidentifiering detektor API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) erbjuder två metoder för avvikelseidentifiering. Du kan antingen identifiera avvikelser som en batch i hela din gånger serien, eller som dina data som genereras av identifiering av avvikelser status för den senaste datapunkten. Identifiering av modellen returnerar avvikelseidentifiering resultat tillsammans med varje datapunkt förväntat värde och avvikelseidentifiering för övre och nedre gränserna för identifiering. Du kan använda dessa värden för att visualisera intervallet för normal värden och inkonsekvenser i data.

## <a name="anomaly-detection-modes"></a>Lägen för identifiering av avvikelser 

API: T för Avvikelseidentifiering detektor får identifiering lägen: batch- och strömning.

> [!NOTE]
> Följande begäran URL: er måste kombineras med lämplig slutpunkt för din prenumeration. Exempel: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Batch-identifiering

Använd följande begärande-URI för att identifiera avvikelser i en batch med datapunkter över en viss tidsperiod med time series-data: 

`/timeseries/entire/detect`. 

Genom att skicka din time series-data på samma gång, ska API: et Generera en modell med hela serien och analysera varje datapunkt med den.  

### <a name="streaming-detection"></a>Strömmande identifiering

Använd följande begärande-URI för att identifiera avvikelser på strömmande data kontinuerligt, med dina senaste datapunkt: 

`/timeseries/last/detect'`. 

Genom att skicka nya datapunkter som du skapa dem, kan du övervaka dina data i realtid. En modell kommer att genereras med datapunkter som du skickar och API: et avgör om den senaste tidpunkten i tidsserien är en avvikelse.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Justera lägre och övre avvikelseidentifiering identifiering av gränser

Som standard övre och nedre gränserna för avvikelseidentifiering beräknas med hjälp av `expectedValue`, `upperMargin`, och `lowerMargin`. Om du behöver olika gränser, rekommenderar vi att tillämpa en `marginScale` till `upperMargin` eller `lowerMargin`. Gränserna kan beräknas enligt följande:

|Gräns  |Beräkning  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

I följande exempel visas ett Avvikelseidentifiering detektor API-resultat på olika sensitivities.

### <a name="example-with-sensitivity-at-99"></a>Exempel med känslighet vid 99

![Standard känslighet](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exempel med känslighet på 95

![99 känslighet](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exempel med känslighet på 85

![85 känslighet](../media/sensitivity_85.png)

## <a name="next-steps"></a>Nästa steg

* [Vad är API: T för Avvikelseidentifiering detektor?](../overview.md)
* [Snabbstart: Identifiera avvikelser i dina time series-data med hjälp av Avvikelseidentifiering detektor REST API](../quickstarts/detect-data-anomalies-csharp.md)