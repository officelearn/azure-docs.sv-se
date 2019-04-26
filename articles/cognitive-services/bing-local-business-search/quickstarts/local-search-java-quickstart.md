---
title: Snabbstart – skicka en fråga till den lokala företag i Bing med hjälp av Java | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln för att börja använda den lokala företag i Bing i Java.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: ed98d40987645fdadff8cc628cfd08feca41d01f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60336039"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Snabbstart: Skicka en fråga till den lokala företag i Bing med hjälp av Java

Använd den här snabbstarten för att börja skicka begäranden till den lokala företag i Bing, vilket är en Azure Cognitive Service. Medan detta enkla program är skriven i Java, är API: et en RESTful-webb-tjänst som är kompatibel med alla programmeringsspråk som HTTP-förfrågningar och JSON-parsning.

Det här exempelprogrammet hämtar lokala svarsdata från API: et för sökfrågan `hotel in Bellevue`.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med API:er för Bing-sökresultat. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Du behöver den åtkomstnyckel som tillhandahålls när du aktiverar din kostnadsfria utvärderingsversion.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Det här exempelprogrammet hämtar lokala svarsdata från frågan för en *hotell i Bellevue*.

## <a name="create-the-request"></a>Skapa begäran 

Följande kod skapar en `WebRequest`anger rubriken åtkomst och lägger till en frågesträng för ”hotell i Bellevue”.  Därefter skickar den en begäran och tilldelar svaret till en sträng som ska innehålla JSON-texten.

```
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>Kör hela appen

Den lokala företag i Bing returnerar resultat från Bing search-motorn.
1. Hämta eller installera gson-biblioteket.
2. Skapa ett nytt Java-projekt i den IDE eller redigerare du föredrar.
3. Lägg till koden nedan.
4. Ersätt värdet för subscriptionKey med en giltig åtkomstnyckel för din prenumeration.
5. Kör programmet.

```
package localSearch;
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
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
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

                SearchResults result = SearchLocal(searchTerm);

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

## <a name="next-steps"></a>Nästa steg
- [Lokala företag Search-Snabbstart](local-quickstart.md)
- [Lokala företag Search-Node-quickstart](local-search-node-quickstart.md)
- [Lokala företag Search Python-Snabbstart](local-search-python-quickstart.md)
