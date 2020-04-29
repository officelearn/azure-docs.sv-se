---
title: 'Snabb start: anropa din Anpassad sökning i Bing-slutpunkt med Java | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att börja begära sökresultat från din instans av anpassad Bing-sökning i Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 02c86e5a4c1a04b98ebba73653980e8e5e00f645
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80238879"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Snabb start: anropa din Anpassad sökning i Bing slut punkt med Java

Använd den här snabbstarten till att börja begära sökresultat från din instans av anpassad Bing-sökning. Även om det här programmet är skrivet i Java är API för anpassad Bing-sökning en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Käll koden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Krav

- En instans av anpassad Bing-sökning. Se [snabb start: skapa din första anpassad sökning i Bing-instans](quick-start.md) för mer information.

- Senaste [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

- [Gson-biblioteket](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Skapa en klass med `CustomSrchJava`namnet och skapa variabler för din prenumerations nyckel, anpassad Sök slut punkt och din Sök Instanss anpassade KONFIGURATIONS-ID. Du kan använda den globala slut punkten nedan eller den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Skapa en annan klass med namnet `SearchResults` som ska innehålla svaret från instansen för Anpassad Bing-sökning.

    ```csharp
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Skapa en funktion med namnet `prettify()` för att formatera JSON-svaret från API för anpassad Bing-sökning.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran 

1. Skapa en funktion med namnet `SearchWeb()` som skickar en begäran och returnerar ett `SearchResults`-objekt. Skapa begärande-URL genom att kombinera informationen om ditt anpassade konfigurations-ID, frågan och slutpunkten. Lägg till din prenumerationsnyckel i `Ocp-Apim-Subscription-Key`-huvudet.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. Skapa en ström och lagra JSON-svaret i ett `SearchResults`-objekt.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. I main-metoden för programmet anropar du `SearchWeb()` med söktermen. 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Kör programmet.
    
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)
