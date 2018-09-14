---
title: 'Snabbstart: Använda Java för att anropa API för webbsökning i Bing'
description: I den här snabbstarten lär du dig att göra ditt första anrop API för webbsökning i Bing med Java och att ta emot ett JSON-svar.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: 8d3e01aef8efdf1503ad7056220e0cba9fb38ed3
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888232"
---
# <a name="quickstart-use-java-to-call-the-bing-web-search-api"></a>Snabbstart: Använda Java för att anropa API för webbsökning i Bing  

Använd den här snabbstarten för att göra ditt första anrop till API för webbsökning i Bing och få ett JSON-svar.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Här följer några saker som du behöver innan du kör den här snabbstarten:

* [JDK 7 eller 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Gson-bibliotek](https://github.com/google/gson)
* En prenumerationsnyckel

## <a name="create-a-project-and-import-dependencies"></a>Skapa ett projekt och importera beroenden

Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek. Gson krävs för att konvertera Java-objekt till JSON.

```java
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
```

### <a name="declare-gson-in-the-maven-pom-file"></a>Deklarera Gson i Maven POM-filen

Om du använder Maven deklarerar du Gson i `POM.xml`. Hoppa över det här steget om du har installerat Gson lokalt.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.1</version>
</dependency>
```

## <a name="declare-the-bingwebsearch-class"></a>Deklarera klassen BingWebSearch

Deklarera klassen `BingWebSearch`. Den innehåller största delen av den kod vi granskar i den här snabbstarten, inklusive `main`-metoden.  

```java
public class BingWebSearch {

// The code in the following sections goes here.

}
```

## <a name="define-variables"></a>Definiera variabler

Den här koden ställer in `subscriptionKey`, `host`, `path` och `searchTerm`. Bekräfta att slutpunkten är korrekt och ersätt värdet `subscriptionKey` med en giltig prenumerationsnyckel från ditt Azure-konto. Du kan anpassa sökfrågan genom att ersätta värdet för `searchTerm`.

```java
// Enter a valid subscription key.
static String subscriptionKey = "enter key here";

/*
 * If you encounter unexpected authorization errors, double-check these values
 * against the endpoint for your Bing Web search instance in your Azure
 * dashboard.
 */
static String host = "https://api.cognitive.microsoft.com";
static String path = "/bing/v7.0/search";
static String searchTerm = "Microsoft Cognitive Services";
```

## <a name="construct-a-request"></a>Skapa en begäran

Den här metoden, som finns i klassen `BingWebSearch`, konstruerar `url`, tar emot och parsar svaret samt extraherar Bing-relaterade HTTP-huvuden.  

```java
public static SearchResults SearchWeb (String searchQuery) throws Exception {
    // Construct the URL.
    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));

    // Open the connection.
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // Receive the JSON response body.
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // Construct the result object.
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);

    // Extract Bing-related HTTP headers.
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")){
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    stream.close();
    return results;
}
```

## <a name="handle-the-response"></a>Hantera svaret

Använd Gson för att parsa och omserialisera svaret.

```java
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="declare-the-main-method"></a>Deklarera main-metoden

Den här metoden krävs och det är den första metoden som anropas när programmet startas. I det här programmet innehåller den kod som verifierar `subscriptionKey`, gör en begäran och skriver ut JSON-svaret.

```java
public static void main (String[] args) {
    // Confirm the subscriptionKey is valid.
    if (subscriptionKey.length() != 32) {
        System.out.println("Invalid Bing Search API subscription key!");
        System.out.println("Please paste yours into the source code.");
        System.exit(1);
    }

    // Call the SearchWeb method and print the response.
    try {
        System.out.println("Searching the Web for: " + searchTerm);
        SearchResults result = SearchWeb(searchTerm);
        System.out.println("\nRelevant HTTP Headers:\n");
        for (String header : result.relevantHeaders.keySet())
        System.out.println(header + ": " + result.relevantHeaders.get(header));
        System.out.println("\nJSON Response:\n");
        System.out.println(prettify(result.jsonResponse));
    }
    catch (Exception e) {
        e.printStackTrace(System.out);
        System.exit(1);
    }
}
```

## <a name="create-a-container-class-for-search-results"></a>Skapa en containerklass för sökresultat

Containerklassen `SearchResults` är utanför klassen `BingWebSearch`. Den innehåller relevanta rubriker och JSON-data för svaret.

```java
class SearchResults{
    HashMap<String, String> relevantHeaders;
    String jsonResponse;
    SearchResults(HashMap<String, String> headers, String json) {
        relevantHeaders = headers;
        jsonResponse = json;
    }
}
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det sista steget är att kompilera koden och köra den! Nedan visas kommandona:

```powershell
javac BingWebSearch.java -classpath ./gson-2.8.1.jar -encoding UTF-8
java -cp ./gson-2.8.1.jar BingWebSearch
```

Om du vill jämföra din kod med vår [finns exempelkoden på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingWebSearchv7.java).

## <a name="sample-response"></a>Exempelsvar

Svar från API för webbsökning i Bing returneras som JSON. Det här exempelsvaret har trunkerats för att visa ett enskilt resultat.

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie om app på en sida för Bing-webbsökning](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]  
