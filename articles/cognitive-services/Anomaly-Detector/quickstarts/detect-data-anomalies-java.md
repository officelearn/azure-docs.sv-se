---
title: 'Snabbstart: Identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API och Java'
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
ms.openlocfilehash: 001d53cbd7e2a57615ea3da71d128bd210a79921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565856"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Snabbstart: Identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API och Java

Använd den här snabb starten för att börja använda de två identifierings lägena för avvikelse detektor API: erna för att identifiera avvikelser i dina tids serie data. Java-programmet skickar två API-begäranden som innehåller JSON-formaterade Time Series-data och hämtar svaren.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svaret som innehåller avvikelse status (och andra data) för varje data punkt i tids serie data och positionerna för identifierade avvikelser. |
| Identifiera avvikelse statusen för den senaste data punkten | JSON-svaret som innehåller avvikelse status (och andra data) för den senaste data punkten i tids serie data.                                                                                                                                         |

 Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller senare.

- Importera de här biblioteken från maven-lagringsplatsen
    - [JSON i Java](https://mvnrepository.com/artifact/org.json/json) -paket
    - [Apache httpclient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -paket

- En JSON-fil som innehåller tids serie data punkter. Exempel data för den här snabb starten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

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

2. Skapa variabler för din prenumerations nyckel och din slut punkt. Nedan visas de URI: er som du kan använda för avvikelse identifiering. Dessa kommer att läggas till i tjänst slut punkten senare för att skapa API-begärandena URL: er.

    |Identifierings metod  |URI  |
    |---------|---------|
    |Batch-identifiering    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste data punkten     | `/anomalydetector/v1.0/timeseries/last/detect`        |

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
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begär Anden

1. Skapa en ny funktion `sendRequest()` som använder variablerna som skapats ovan. Utför sedan följande steg.

2. Skapa ett `CloseableHttpClient` objekt som kan skicka begär anden till API: et. Skicka begäran till ett `HttpPost` Request-objekt genom att kombinera slut punkten och en URL för avvikelse identifiering.

3. Använd begärans `setHeader()` funktion för att `Content-Type` ange rubriken till `application/json`och Lägg till din prenumerations nyckel i `Ocp-Apim-Subscription-Key` rubriken.

4. Använd begärans `setEntity()` funktion för att skicka de data som ska skickas.

5. Använd klientens `execute()` funktion för att skicka begäran och spara den till ett `CloseableHttpResponse` objekt.

6. Skapa ett `HttpEntity` objekt för att lagra svars innehållet. Hämta innehållet med `getEntity()`. Returnera det om svaret inte är tomt.

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

1. Skapa en metod som `detectAnomaliesBatch()` kallas för att identifiera avvikelser i alla data som en batch. `sendRequest()` Anropa metoden som skapades ovan med din slut punkt, URL, prenumerations nyckel och JSON-data. Hämta resultatet och skriv ut det till-konsolen.

2. Om fältet svar innehåller `code` skriver du ut felkoden och fel meddelandet.

3. Annars hittar du positionerna för avvikelser i data uppsättningen. Svarets `isAnomaly` fält innehåller ett booleskt värde som relaterar till om en viss data punkt är en avvikelse. Hämta JSON-matrisen och upprepa den genom att skriva ut indexet för `true` alla värden. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

* Skapa en metod som `detectAnomaliesLatest()` kallas för att identifiera avvikelse status för den sista data punkten i data uppsättningen. `sendRequest()` Anropa metoden som skapades ovan med din slut punkt, URL, prenumerations nyckel och JSON-data. Hämta resultatet och skriv ut det till-konsolen.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Läs in dina Time Series-data och skicka begäran

1. I appens huvud metod läser du i JSON-filen som innehåller de data som ska läggas till i begäran.

2. Anropa de två avvikelse identifierings funktionerna som skapats ovan.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan om du vill visa JSON-svaret på GitHub:
* [Exempel svar för batch-identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exempel svar på senaste Poäng identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
