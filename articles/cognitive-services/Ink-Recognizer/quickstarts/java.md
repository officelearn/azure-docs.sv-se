---
title: 'Snabbstart: Identifiera digitalt bläck med hand SKRIFTS tolken REST API och Java'
titleSuffix: Azure Cognitive Services
description: Använd pennan tecknings tolkens API för att börja identifiera digitala penndrag.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 36ff0fe4550b140a722ed25f4e372f7c88581211
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212684"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Snabbstart: Identifiera digitalt bläck med hand SKRIFTS tolken REST API och Java

Använd den här snabb starten för att börja använda pennan tecknings tolkens API på digitala penndrag. Det här Java-programmet skickar en API-begäran som innehåller JSON-formaterade penndrag och hämtar svaret.

Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Normalt anropar du API: et från en digital intecknings app. I den här snabb starten skickas Penn strecks data för följande handskrivna exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Du hittar käll koden för den här snabb starten på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Förutsättningar

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller senare.

- Importera de här biblioteken från maven-lagringsplatsen
    - [JSON i Java](https://mvnrepository.com/artifact/org.json/json) -paket
    - [Apache httpclient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -paket

- Du hittar exempel Penn strecks data för den här snabb starten på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

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

2. Skapa variabler för din prenumerations nyckel och din slut punkt. Ersätt slut punkten nedan med den som har genererats för din tryck färgs igenkännings resurs. Lägg till den i hand SKRIFTS tolkens URI för att ansluta till API: et.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://<your-custom-subdomain>.cognitiveservices.azure.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begär Anden

1. Skapa en ny funktion `sendRequest()` som använder variablerna som skapats ovan. Utför sedan följande steg.

2. Skapa ett `CloseableHttpClient` objekt som kan skicka begär anden till API: et. Skicka begäran till ett `HttpPut` Request-objekt genom att kombinera slut punkten och färg tolknings-URL: en.

3. Använd begärans `setHeader()` funktion för att `Content-Type` ange rubriken till `application/json`och Lägg till din prenumerations nyckel i `Ocp-Apim-Subscription-Key` rubriken.

4. Använd begärans `setEntity()` funktion för att skicka de data som ska skickas.   

5. Använd klientens `execute()` funktion för att skicka begäran och spara den till ett `CloseableHttpResponse` objekt. 

6. Skapa ett `HttpEntity` objekt för att lagra svars innehållet. Hämta innehållet med `getEntity()`. Returnera det om svaret inte är tomt.
    
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

## <a name="send-an-ink-recognition-request"></a>Skicka en begäran om hand SKRIFTS igenkänning

Skapa en metod som `recognizeInk()` kallas för att identifiera Penn strecks data. `sendRequest()` Anropa metoden som skapades ovan med din slut punkt, URL, prenumerations nyckel och JSON-data. Hämta resultatet och skriv ut det till-konsolen.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Läs in dina digitala pennan tecknings data och skicka begäran

1. I appens huvud metod läser du i JSON-filen som innehåller de data som ska läggas till i begäran.

2. Anropa funktionen för tryck färgs igenkänning som skapats ovan.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Kör programmet och Visa svaret

Kör appen. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svaret på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://go.microsoft.com/fwlink/?linkid=2089907)


Ta en titt på följande exempel program på GitHub för att se hur API: et för färg igenkänning fungerar i en digital inkungs app:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
