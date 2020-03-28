---
title: 'Snabbstart: Identifiera avvikelser som en batch med REST API för avvikelsedetektor och Python'
titleSuffix: Azure Cognitive Services
description: Använd API:et för avvikelsedetektor för att identifiera avvikelser i dataserien, antingen som en batch eller på strömmande data i den här snabbstarten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 684aba561dc50b64dd7cc564cff8e55229ce1429
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239032"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Snabbstart: Identifiera avvikelser i dina tidsseriedata med rest-APIn för avvikelsedetektorn och Python

Använd den här snabbstarten för att börja använda API:et för avvikelsedetektorns två identifieringslägen för att identifiera avvikelser i tidsseriedata. Det här Python-programmet skickar två API-begäranden som innehåller JSON-formaterade tidsseriedata och får svaren.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svaret som innehåller avvikelsestatus (och andra data) för varje datapunkt i tidsseriedata och positionerna för identifierade avvikelser. |
| Identifiera avvikelsestatus för den senaste datapunkten | JSON-svaret som innehåller avvikelsestatus (och andra data) för den senaste datapunkten i tidsseriedata.                                                                                                                                         |

 Även om det här programmet är skrivet i Python, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Du hittar källkoden för den här snabbstarten på [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Krav

- [Python 2.x eller 3.x](https://www.python.org/downloads/)
- En avvikelsedetektornyckel och slutpunkt
- [Biblioteket Begär](https://pypi.org/project/requests/) för python

- En JSON-fil som innehåller tidsseriedatapunkter. Exempeldata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Skapa en avvikelsedetektorresurs

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny python-fil och lägg till följande import.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Skapa variabler för din prenumerationsnyckel och din slutpunkt. Nedan finns de URI:er som du kan använda för avvikelseidentifiering. Dessa läggs till i tjänstslutpunkten senare för att skapa URL:erna för API-begäran.

    |Identifieringsmetod  |URI  |
    |---------|---------|
    |Identifiering av batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste datapunkten     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Läs i JSON-datafilen genom att `json.load()`öppna den och använda .

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny `send_request()` funktion som kallas som tar de variabler som skapats ovan. Utför sedan följande steg.

2. Skapa en ordlista för förfrågerubrikerna. Ställ `Content-Type` in `application/json`på och lägg till `Ocp-Apim-Subscription-Key` prenumerationsnyckeln i sidhuvudet.

3. Skicka begäran `requests.post()`med . Kombinera url:en för slutpunkt och avvikelseidentifiering för url:en för fullständig begäran och inkludera dina rubriker och json-begärandedata. Och sedan tillbaka svaret.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en `detect_batch()` metod som anropas för att identifiera avvikelser i data som en batch. Anropa `send_request()` den metod som skapats ovan med slutpunkt, url, prenumerationsnyckel och json-data.

2. Anrop `json.dumps()` på resultatet för att formatera det och skriv ut det på konsolen.

3. Om svaret innehåller `code` fält skriver du ut felkoden och felmeddelandet.

4. Annars hittar du positionerna för avvikelser i datauppsättningen. Svarets `isAnomaly` fält innehåller ett booleskt värde som relaterar till om en viss datapunkt är en avvikelse. Iterera genom listan och skriv ut `True` index för eventuella värden. Dessa värden motsvarar index för avvikande datapunkter, om några hittades.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelsestatus för den senaste datapunkten

1. Skapa en `detect_latest()` metod som anropas för att avgöra om den senaste datapunkten i tidsserien är en avvikelse. Anropa `send_request()` metoden ovan med slutpunkt, url, prenumerationsnyckel och json-data. 

2. Anrop `json.dumps()` på resultatet för att formatera det och skriv ut det på konsolen.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>Skicka begäran

Anropa de metoder för avvikelseidentifiering som skapats ovan.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan om du vill visa JSON-svaret på GitHub:
* [Exempel på batchidentifieringssvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exempel på senaste punktidentifieringssvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
