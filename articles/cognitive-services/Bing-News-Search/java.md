---
title: 'Snabbstart: API för nyhetssökning i Bing, Java'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med att använda API:et för nyhetssökning i Bing i Microsoft Cognitive Services på Azure.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: d8ab0e10e8332cf2305185849d6446d679210845
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316845"
---
# <a name="quickstart-for-bing-news-search-api-with-java"></a>Snabbstart för API:et för nyhetssökning i Bing med Java

Den här artikeln visar hur använder API:n för Bing-sökning, som ingår i Microsoft Cognitive Services i Azure. Även om den här artikeln använder Java, är API:n en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 

Exempelkoden skrivs att köras under Java 7 som ett konsolprogram.

Teknisk information om API:erna finns i [API-referensen](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API:er för Bing-sökresultat**. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Du behöver den åtkomstnyckel som du fick när du aktiverade din kostnadsfria utvärderingsversion, eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure. Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="bing-news-search"></a>Nyhetssökning i Bing

[API:et för nyhetssökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) returnerar nyhetsresultat från Bings sökmotor.

1. Hämta eller installera [gson-biblioteket](https://github.com/google/gson).
2. Skapa ett nytt Java-projekt i den IDE eller redigerare du föredrar.
3. Lägg till koden nedan.
4. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
5. Kör programmet.

```java
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingNewsSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingNewsSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar BingNewsSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class BingNewsSearch {

// ***********************************************
// *** Update or verify the following values. ***
// **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "enter key here";

    // Verify the endpoint URI.  At this writing, only one endpoint is used for Bing
    // search APIs.  In the future, regional endpoints may be available.  If you
    // encounter unexpected authorization errors, double-check this value against
    // the endpoint for your Bing Web search instance in your Azure dashboard.
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/news/search";

    static String searchTerm = "Microsoft";

    public static SearchResults SearchNews (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchNews(searchTerm);

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
}

// Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
    HashMap<String, String> relevantHeaders;
    String jsonResponse;
    SearchResults(HashMap<String, String> headers, String json) {
        relevantHeaders = headers;
        jsonResponse = json;
    }
}
```

**Svar**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .
      
      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Nyheter för sidindelning](paging-news.md)
> [Använd dekorationsmarkörer för att markera text](hit-highlighting.md)
> [Söka på webben efter nyheter](search-the-web.md)   
> [Prova](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)

