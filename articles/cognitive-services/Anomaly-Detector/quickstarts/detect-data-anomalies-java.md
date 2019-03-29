---
title: 'Snabbstart: Identifiera avvikelser i dina time series-data med hjälp av REST-API för Avvikelseidentifiering detektor och Java | Microsoft Docs'
description: 'Använda API: T för Avvikelseidentifiering detektor för att identifiera avvikelser i dina dataserien som en batch eller på strömmande data.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 06cb4d32359014f3cbc67ed1f75988c794e6599e
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619523"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Snabbstart: Identifiera avvikelser i dina time series-data med hjälp av REST-API för Avvikelseidentifiering detektor och Java

Använd den här snabbstarten för att börja använda identifiering av avvikelser detektor API: er lägena för att identifiera avvikelser i tidsseriedata. Det här Java-programmet skickar två API-begäranden som innehåller JSON-formaterade time series-data, och får svar.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svar som innehåller avvikelseidentifiering status (och andra data) för varje datapunkt i time series-data och positioner för alla identifierade avvikelser. |
| Identifiera avvikelser status för senaste datapunkt | JSON-svar som innehåller avvikelseidentifiering status (och andra data) för den senaste datapunkten i time series-data.                                                                                                                                         |

 Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

- Den [Java&trade; utveckling Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller senare.

- Importera dessa bibliotek från Maven-centrallagret
    - [JSON i Java](https://mvnrepository.com/artifact/org.json/json) paket
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) paket

- Pekar en JSON-fil som innehåller time series-data. Exempeldata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Skapa variabler för din prenumerationsnyckel och slutpunkten. Nedan visas URI: er som du kan använda för identifiering av avvikelser. Dessa kommer att läggas till tjänstens slutpunkt senare för att skapa API: et begär URL: er.

    |Identifieringsmetod  |URI  |
    |---------|---------|
    |Batch-identifiering    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste tidpunkten för data     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Läs i JSON-datafilen

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny funktion som kallas `sendRequest()` som tar de variabler som skapades ovan. Utför sedan följande steg.

2. Skapa en `CloseableHttpClient` objekt som kan skicka begäranden till API: et. Skicka begäran till en `HttpPost` objekt genom att kombinera din slutpunkt och en Avvikelseidentifiering detektor URL.

3. Använd en förfrågan `setHeader()` funktionen för att ange den `Content-Type` sidhuvud till `application/json`, och Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik.

4. Använd en förfrågan `setEntity()` att data skickas.   

5. Använda klientens `execute()` för att skicka begäran och spara den i en `CloseableHttpResponse` objekt. 

6. Skapa en `HttpEntity` objekt för att lagra svarsinnehållet. Hämta innehåll med `getEntity()`. Om svaret är inte tom, returnera den.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en metod som kallas `detectAnomaliesBatch()` att identifiera avvikelser i hela datamängden som en batch. Anropa den `sendRequest()` metod som skapades ovan med din slutpunkt, URL: en, prenumerationsnyckel och json-data. Få resultat och skriva ut den till konsolen.

2. Hitta positioner av avvikelser i datauppsättningen. Svarets `isAnomaly` fält innehåller ett booleskt värde som är relaterade till om en viss datapunkt är en avvikelse. Hämta JSON-matris och gå igenom den, skriva ut index för någon `true` värden. Dessa värden motsvarar index för avvikande datapunkter, om några.

    
    ```java
    static void detectAnomaliesBatch(String requestData) {
        System.out.println("Detecting anomalies as a batch");
        String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
        if (result != null) {
            System.out.println(result);
            JSONObject jsonObj = new JSONObject(result);
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
    ```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelser status för senaste datapunkt

* Skapa en metod som kallas `detectAnomaliesLatest()` att identifiera avvikelser status för den sista datapunkten i datauppsättningen. Anropa den `sendRequest()` metod som skapades ovan med din slutpunkt, URL: en, prenumerationsnyckel och json-data. Få resultat och skriva ut den till konsolen.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Läsa in time series-data och skicka begäran

1. Läs i JSON-fil som innehåller de data som kommer att läggas till begäranden i ditt program main-metoden.

2. Anropa två avvikelseidentifiering identifiering av funktioner som skapades ovan.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        detectAnomaliesBatch(requestData);
        detectAnomaliesLatest(requestData);
    }
    ```

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan för att visa JSON-svar på GitHub:
* [Exempelsvar batch identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Senaste återställningspunkt identifiering exempelsvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)