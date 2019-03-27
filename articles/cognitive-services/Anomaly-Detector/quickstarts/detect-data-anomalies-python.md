---
title: 'Snabbstart: Identifiera avvikelser som en batch med hjälp av REST-API för Avvikelseidentifiering detektor och Python | Microsoft Docs'
description: 'Använda API: T för Avvikelseidentifiering detektor för att identifiera avvikelser i dina dataserien som en batch eller på strömmande data.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 60307d51439b4474c8be4f040792c03a6f83b0fd
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473275"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Snabbstart: Identifiera avvikelser i tidsseriedata med Avvikelseidentifiering detektor REST API och Python

Använd den här snabbstarten för att börja använda identifiering av avvikelser detektor API: er lägena för att identifiera avvikelser i tidsseriedata. Den här Python-program skickar två API-begäranden som innehåller JSON-formaterade time series-data, och får svar.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svar som innehåller avvikelseidentifiering status (och andra data) för varje datapunkt i time series-data och positioner för alla identifierade avvikelser. |
| Identifiera avvikelser status för senaste datapunkt | JSON-svar som innehåller avvikelseidentifiering status (och andra data) för den senaste datapunkten i time series-data.                                                                                                                                         |

 Även om det här programmet är skrivet i Python, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

- [Python 2.x eller 3.x](https://www.python.org/downloads/)

- Den [begäranden biblioteket](http://docs.python-requests.org) för python

- Pekar en JSON-fil som innehåller time series-data. Exempeldata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny python-fil i ditt favoritprogram för textredigering eller IDE. Lägg till följande importer.

    ```python
    import requests
    import json
    ```

2. Skapa variabler för din prenumerationsnyckel och slutpunkten. Nedan visas URI: er som du kan använda för identifiering av avvikelser. Dessa kommer att läggas till tjänstens slutpunkt senare för att skapa API: et begär URL: er.

    |Identifieringsmetod  |URI  |
    |---------|---------|
    |Batch-identifiering    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste tidpunkten för data     | `/anomalydetector/v1.0/timeseries/last/detect`        |

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

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny funktion som kallas `send_request()` som tar de variabler som skapades ovan. Utför sedan följande steg.

2. Skapa en ordlista för de begärda rubrikerna. Ange den `Content-Type` till `application/json`, och Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik.

3. Skicka en begäran med hjälp av `requests.post()`. Kombinera din slutpunkt och avvikelseidentifiering identifiering av URL: en för fullständiga URL för begäran och inkluderar sidhuvuden, och data för json-begäran. 

4. Om begäran lyckas returnera svaret.  
    
    ```python
    def send_request(endpoint, url, subscription_key, request_data):
        headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
        response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
        if response.status_code == 200:
            return json.loads(response.content.decode("utf-8"))
        else:
            print(response.status_code)
            raise Exception(response.text)
    ```

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en metod som kallas `detect_batch()` att identifiera avvikelser i hela datamängden som en batch. Anropa den `send_request()` metod som skapades ovan med din slutpunkt, URL: en, prenumerationsnyckel och json-data. 

2. Anropa `json.dumps()` på resultatet att formatera den och skriva ut den till konsolen.

3. Hitta positioner av avvikelser i datauppsättningen. Svarets `isAnomaly` fält innehåller ett booleskt värde som är relaterade till om en viss datapunkt är en avvikelse. Gå igenom listan och skriva ut index för någon `True` värden. Dessa värden motsvarar index för avvikande datapunkter, om några.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    # Find and display the positions of anomalies in the data set
    anomalies = result["isAnomaly"]
    print("Anomalies detected in the following data positions:")
    for x in range(len(anomalies)):
        if anomalies[x] == True:
            print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelser status för senaste datapunkt

1. Skapa en metod som kallas `detect_latest()` att avgöra om den senaste tidpunkten för data i din tidsserier är en avvikelse. Anropa den `send_request()` metoden ovan med din slutpunkt, URL: en, prenumerationsnyckel och json-data. 

2. Anropa `json.dumps()` på resultatet att formatera den och skriva ut den till konsolen.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Läsa in time series-data och skicka begäran

1. Läsa in din JSON time series-data att öppna en filhanterare och använda `json.load()` på den. Anropa sedan avvikelsen identifieringsmetoder som skapades ovan.
    
    ```python
    file_handler = open (data_location)
    json_data = json.load(file_handler)
    
    detect_batch(json_data)
    detect_latest(json_data)
    ```

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan för att visa JSON-svar på GitHub:
* [Exempelsvar batch identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Senaste återställningspunkt identifiering exempelsvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)