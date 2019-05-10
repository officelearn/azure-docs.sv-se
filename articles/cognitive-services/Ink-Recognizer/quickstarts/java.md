---
title: 'Snabbstart: Identifiera digitala ink med Ink Igenkännande REST API och Java'
description: Använd Ink Igenkännande API för att starta eftersom digitala ink linjer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6237253922544dc47bb11aec4dd58139f99eb0da
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518618"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Snabbstart: Identifiera digitala ink med Ink Igenkännande REST API och Java

Använd den här snabbstarten för att börja använda API: et för pennanteckningar Igenkännande på digitala ink linjer. Det här Java-programmet skickar en API-begäran som innehåller JSON-formaterade ink linje data och hämtar svaret.

Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Vanligtvis skulle du anropa API från en digital digital penna app. Den här snabbstarten skickar ink linje data för följande handskriven exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Källkoden för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Nödvändiga komponenter

- Den [Java&trade; utveckling Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller senare.

- Importera dessa bibliotek från Maven-centrallagret
    - [JSON i Java](https://mvnrepository.com/artifact/org.json/json) paket
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) paket

- Exempel ink linje data för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Skapa variabler för din prenumerationsnyckel och slutpunkten. Nedan visas den URI som du kan använda för pennanteckningar erkännande. Det kommer att läggas till tjänstens slutpunkt senare för att skapa API-begäran-URL.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny funktion som kallas `sendRequest()` som tar de variabler som skapades ovan. Utför sedan följande steg.

2. Skapa en `CloseableHttpClient` objekt som kan skicka begäranden till API: et. Skicka begäran till en `HttpPut` objekt genom att kombinera din slutpunkt och URL: en för pennanteckningar Igenkännande.

3. Använd en förfrågan `setHeader()` funktionen för att ange den `Content-Type` sidhuvud till `application/json`, och Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik.

4. Använd en förfrågan `setEntity()` att data skickas.   

5. Använda klientens `execute()` för att skicka begäran och spara den i en `CloseableHttpResponse` objekt. 

6. Skapa en `HttpEntity` objekt för att lagra svarsinnehållet. Hämta innehåll med `getEntity()`. Om svaret är inte tom, returnera den.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
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
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Skicka en begäran om ink taligenkänning

Skapa en metod som kallas `recognizeInk()` ska kunna identifiera dina ink linje data. Anropa den `sendRequest()` metod som skapades ovan med din slutpunkt, URL: en, prenumerationsnyckel och json-data. Få resultat och skriva ut den till konsolen.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Läsa in dina digitala ink-data och skicka begäran

1. Läs i JSON-fil som innehåller de data som kommer att läggas till begäranden i ditt program main-metoden.

2. Anropa funktionen ink erkännande skapade ovan.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Kör programmet och visa svaret

Kör appen. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svar på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://go.microsoft.com/fwlink/?linkid=2089907)


Om du vill se hur den Ink-API: T fungerar i en digital digital penna app, ta en titt på de följande exempelprogram på GitHub:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
