---
title: 'Snabb start: identifiera avvikelser som en batch med hjälp av avvikelse detektor REST API och python'
titleSuffix: Azure Cognitive Services
description: 'Använd API: t för avvikelse detektor för att identifiera avvikelser i din data serie antingen som en batch eller strömmande data i den här snabb starten.'
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 0fd82cf6c63e3cae811885e0cbe48e4b3dae0f12
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019825"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Snabb start: identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API och python

Använd den här snabb starten för att börja använda de två identifierings lägena för avvikelse detektor API: erna för att identifiera avvikelser i dina tids serie data. Detta python-program skickar API-begäranden som innehåller JSON-formaterade Time Series-data och hämtar svaren.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svaret som innehåller avvikelse status (och andra data) för varje data punkt i tids serie data och positionerna för identifierade avvikelser. |
| Identifiera avvikelse statusen för den senaste data punkten | JSON-svaret som innehåller avvikelse status (och andra data) för den senaste data punkten i tids serie data.|
| Identifiera ändrings punkter som markerar nya data trender | JSON-svaret som innehåller de identifierade ändrings punkterna i tids serie data. |

 Även om det här programmet är skrivet i Python, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Du hittar käll koden för den här snabb starten på [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
- När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i den Azure Portal för att hämta nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    - Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
- [Python 2. x eller 3. x](https://www.python.org/downloads/)
- [Bibliotek för begär Anden](https://pypi.org/project/requests/) för python

- En JSON-fil som innehåller tids serie data punkter. Exempel data för den här snabb starten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny python-fil och Lägg till följande importer.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Skapa variabler för din prenumerations nyckel och din slut punkt. Nedan visas de URI: er som du kan använda för avvikelse identifiering. Dessa kommer att läggas till i tjänst slut punkten senare för att skapa API-begärandena URL: er.

    |Identifieringsmetod  |URI  |
    |---------|---------|
    |Batch-identifiering    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste data punkten     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | Identifiering av ändrings punkt | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Läs i JSON-datafilen genom att öppna den och använda `json.load()` .

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begär Anden

1. Skapa en ny funktion `send_request()` som använder variablerna som skapats ovan. Utför sedan följande steg.

2. Skapa en ord lista för begärandehuvuden. Ange `Content-Type` till `application/json` och Lägg till din prenumerations nyckel i `Ocp-Apim-Subscription-Key` rubriken.

3. Skicka begäran med hjälp av `requests.post()` . Kombinera URL: en för slut punkt och avvikelse identifiering för URL: en för fullständig begäran och inkludera dina sidhuvud och data för JSON-begäran. Och returnerar sedan svaret.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en metod `detect_batch()` som kallas för att identifiera avvikelser i alla data som en batch. Anropa `send_request()` metoden som skapades ovan med din slut punkt, URL, prenumerations nyckel och JSON-data.

2. Anropa `json.dumps()` resultatet för att formatera det och skriva ut det till-konsolen.

3. Om fältet svar innehåller `code` skriver du ut felkoden och fel meddelandet.

4. Annars hittar du positionerna för avvikelser i data uppsättningen. Svarets `isAnomaly` fält innehåller ett booleskt värde som relaterar till om en viss data punkt är en avvikelse. Upprepa i listan och skriv ut indexet för alla `True` värden. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

1. Skapa en metod `detect_latest()` som kallas för att avgöra om den senaste data punkten i tids serien är en avvikelse. Anropa `send_request()` metoden ovan med din slut punkt, URL, prenumerations nyckel och JSON-data.

2. Anropa `json.dumps()` resultatet för att formatera det och skriva ut det till-konsolen.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="detect-change-points-in-the-data"></a>Identifiera ändrings punkter i data

1. Skapa en metod `detect_change_point()` som kallas för att identifiera avvikelser i alla data som en batch. Anropa `send_request()` metoden som skapades ovan med din slut punkt, URL, prenumerations nyckel och JSON-data.

2. Anropa `json.dumps()` resultatet för att formatera det och skriva ut det till-konsolen.

3. Om svaret innehåller ett `code` fält skriver du ut felkoden och fel meddelandet.

4. Annars hittar du positionerna för avvikelser i data uppsättningen. Svarets `isChangePoint` fält innehåller ett booleskt värde som anger om en viss data punkt är en avvikelse. Upprepa i listan och skriv ut indexet för alla `True` värden. Dessa värden motsvarar indexen för trend ändrings punkter, om sådana finns.

    [!code-python[detect change points](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectChangePoint)]

## <a name="send-the-request"></a>Skicka begäran

Anropa de metoder för avvikelse identifiering som skapats ovan.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan om du vill visa JSON-svaret på GitHub:
* [Exempel svar för batch-identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exempel svar på senaste Poäng identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Exempel svar för ändring av Poäng identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
