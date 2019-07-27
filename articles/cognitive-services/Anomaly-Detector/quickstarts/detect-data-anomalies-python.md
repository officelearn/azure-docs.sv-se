---
title: 'Snabbstart: Identifiera avvikelser som en batch med hjälp av avvikelse detektor REST API och python'
titleSuffix: Azure Cognitive Services
description: 'Använd API: t för avvikelse detektor för att identifiera avvikelser i din data serie antingen som en batch eller vid strömmande data.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: f40f1b94b3e7c2732fd8bed0bc6e503277b533c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565812"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Snabbstart: Identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API och python

Använd den här snabb starten för att börja använda de två identifierings lägena för avvikelse detektor API: erna för att identifiera avvikelser i dina tids serie data. Detta python-program skickar två API-begäranden som innehåller JSON-formaterade Time Series-data och hämtar svaren.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svaret som innehåller avvikelse status (och andra data) för varje data punkt i tids serie data och positionerna för identifierade avvikelser. |
| Identifiera avvikelse statusen för den senaste data punkten | JSON-svaret som innehåller avvikelse status (och andra data) för den senaste data punkten i tids serie data.                                                                                                                                         |

 Även om det här programmet är skrivet i Python, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

- [Python 2.x eller 3.x](https://www.python.org/downloads/)

- [Bibliotek för begär Anden](http://docs.python-requests.org) för python

- En JSON-fil som innehåller tids serie data punkter. Exempel data för den här snabb starten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny python-fil i din favorit text redigerare eller IDE. Lägg till följande importer.

    ```python
    import requests
    import json
    ```

2. Skapa variabler för din prenumerations nyckel och din slut punkt. Nedan visas de URI: er som du kan använda för avvikelse identifiering. Dessa kommer att läggas till i tjänst slut punkten senare för att skapa API-begärandena URL: er.

    |Identifierings metod  |URI  |
    |---------|---------|
    |Batch-identifiering    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste data punkten     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Läs i JSON-datafilen genom att öppna den och använda `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begär Anden

1. Skapa en ny funktion `send_request()` som använder variablerna som skapats ovan. Utför sedan följande steg.

2. Skapa en ord lista för begärandehuvuden. `Content-Type` Ange till `application/json`och Lägg till `Ocp-Apim-Subscription-Key` din prenumerations nyckel i rubriken.

3. Skicka begäran med hjälp `requests.post()`av. Kombinera URL: en för slut punkt och avvikelse identifiering för URL: en för fullständig begäran och inkludera dina sidhuvud och data för JSON-begäran. Och returnerar sedan svaret.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en metod som `detect_batch()` kallas för att identifiera avvikelser i alla data som en batch. `send_request()` Anropa metoden som skapades ovan med din slut punkt, URL, prenumerations nyckel och JSON-data.

2. Anropa `json.dumps()` resultatet för att formatera det och skriva ut det till-konsolen.

3. Om fältet svar innehåller `code` skriver du ut felkoden och fel meddelandet.

4. Annars hittar du positionerna för avvikelser i data uppsättningen. Svarets `isAnomaly` fält innehåller ett booleskt värde som relaterar till om en viss data punkt är en avvikelse. Upprepa i listan och skriv ut indexet för alla `True` värden. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

1. Skapa en metod som `detect_latest()` kallas för att avgöra om den senaste data punkten i tids serien är en avvikelse. `send_request()` Anropa metoden ovan med din slut punkt, URL, prenumerations nyckel och JSON-data. 

2. Anropa `json.dumps()` resultatet för att formatera det och skriva ut det till-konsolen.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Läs in dina Time Series-data och skicka begäran

1. Läs in dina JSON Time Series-data öppna en fil hanterare och `json.load()` Använd på den. Anropa sedan de avvikelse identifierings metoder som skapats ovan.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan om du vill visa JSON-svaret på GitHub:
* [Exempel svar för batch-identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exempel svar på senaste Poäng identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
