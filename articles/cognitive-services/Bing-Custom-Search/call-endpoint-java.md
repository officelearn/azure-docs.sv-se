---
title: Anropa slutpunkten med hjälp av kognitiva Java - Bing anpassad sökning - Microsoft-tjänster
description: Den här snabbstarten visar hur du begär sökresultat från anpassad sökning-instans med hjälp av Java för att anropa Bing anpassad sökning slutpunkten.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 03d622e3c7a3315238f2bceedae529bbe06af299
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355740"
---
# <a name="call-bing-custom-search-endpoint-java"></a>Anropa Bing anpassad sökning slutpunkt (Java)

Den här snabbstarten visar hur du begär sökresultat från anpassad sökning-instans med hjälp av Java för att anropa Bing anpassad sökning slutpunkten. 

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att slutföra den här snabbstarten:
- En anpassad sökning-instans. Se [skapa din första Bing anpassad sökning instans](quick-start.md).

- [Java](https://www.java.com) installerad.

- En [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing Search**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) är tillräcklig för den här snabbstarten. Du måste åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumeration nyckel från instrumentpanelen i Azure.

## <a name="run-the-code"></a>Kör koden

Följ dessa steg för att anropa Bing anpassad sökning-slutpunkten:

1. Skapa ett paket med din Java IDE föredrar.
2. Skapa filen CustomSrchJava.java och kopiera följande kod till den.
3. Ersätt **din PRENUMERATION nyckel** och **din-anpassad-CONFIG-ID** med din nyckel och konfiguration-ID.

    ``` Java
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
    
    public class CustomSrchJava {       
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";  
    
        static String searchTerm = "Microsoft";  // Replace with search term specific to your defined sources.
    
        public static SearchResults SearchImages (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
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
            if (subscriptionKey.length() != 32) {
                System.out.println("Invalid Bing Search API subscription key!");
                System.out.println("Please paste yours into the source code.");
                System.exit(1);
            }
    
            try {
                System.out.println("Searching the Web for: " + searchTerm);
    
                SearchResults result = SearchImages(searchTerm);
    
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
4. Kör programmet.
    
## <a name="next-steps"></a>Nästa steg
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Markera text med hjälp av decoration markörer](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)