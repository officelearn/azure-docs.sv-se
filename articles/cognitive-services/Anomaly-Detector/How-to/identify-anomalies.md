---
title: Så här använder du API:et för avvikelsedetektorer på tidsseriedata
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar avvikelser i dina data antingen som en batch eller på strömmande data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840227"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Så här: Använd API:et för avvikelsedetektor på tidsseriedata  

[Api:et för avvikelsedetektorer](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) innehåller två metoder för avvikelseidentifiering. Du kan antingen identifiera avvikelser som en batch i hela tidsserien eller när dina data genereras genom att identifiera avvikelsestatus för den senaste datapunkten. Identifieringsmodellen returnerar avvikelseresultat tillsammans med varje datapunkts förväntade värde och de övre och nedre avvikelseidentifieringsgränserna. Du kan använda dessa värden för att visualisera intervallet för normalvärden och avvikelser i data.

## <a name="anomaly-detection-modes"></a>Identifieringslägen för avvikelse 

API:et för avvikelsedetektorn ger identifieringslägen: batch och strömning.

> [!NOTE]
> Följande url:er för begäran måste kombineras med lämplig slutpunkt för din prenumeration. Exempel: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Identifiering av batch

Om du vill identifiera avvikelser i en grupp datapunkter över ett visst tidsintervall använder du följande URI för begäran med tidsseriedata: 

`/timeseries/entire/detect`. 

Genom att skicka tidsseriedata på en gång genererar API:et en modell med hela serien och analyserar varje datapunkt med den.  

### <a name="streaming-detection"></a>Identifiering av direktuppspelning

Om du kontinuerligt vill identifiera avvikelser på strömmande data använder du följande URI för begäran med din senaste datapunkt: 

`/timeseries/last/detect'`. 

Genom att skicka nya datapunkter när du genererar dem kan du övervaka dina data i realtid. En modell genereras med de datapunkter du skickar och API:et avgör om den senaste punkten i tidsserien är en avvikelse.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Justera gränser för identifiering av lägre och övre anomali

Som standard beräknas de övre och nedre gränserna `expectedValue`för `upperMargin`avvikelseidentifiering med hjälp av , och `lowerMargin`. Om du behöver olika gränser rekommenderar `marginScale` `upperMargin` vi `lowerMargin`att du tillämpar en till eller . Gränserna skulle beräknas på följande sätt:

|Gränsen  |Beräkning  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Följande exempel visar ett API för avvikelsedetektor vid olika känsligheter.

### <a name="example-with-sensitivity-at-99"></a>Exempel med känslighet vid 99

![Standardkänslighet](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exempel med känslighet vid 95

![99 Känslighet](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exempel med känslighet vid 85

![85 Känslighet](../media/sensitivity_85.png)

## <a name="next-steps"></a>Efterföljande moment

* [Vad är API:et för avvikelseidentifiering?](../overview.md)
* [Snabbstart: Identifiera avvikelser i tidsseriedata med REST API:et för avvikelsedetektor](../quickstarts/detect-data-anomalies-csharp.md)
