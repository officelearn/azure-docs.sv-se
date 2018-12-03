---
title: 'Snabbstart: Anropa slutpunkt med hjälp av Java – Anpassad sökning i Bing'
titlesuffix: Azure Cognitive Services
description: Den här snabbstarten visar hur du begär sökresultat från din anpassade sökinstans genom att använda Java för att anropa slutpunkten för Anpassad sökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: 5b4494f7840dd9b32cad88ecda800e4dac9c4d8a
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52315927"
---
# <a name="quickstart-call-bing-custom-search-endpoint-java"></a>Snabbstart: Anropa slutpunkten för Anpassad sökning i Bing (Java)

Den här snabbstarten visar hur du begär sökresultat från din anpassade sökinstans genom att använda Java för att anropa slutpunkten för Anpassad sökning i Bing. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

- En anpassad sökinstans som är redo att användas. Se sidan om att [skapa din första instans för Anpassad sökning i Bing](quick-start.md).
- [Java](https://www.java.com) installerat.
- En prenumerationsnyckel. Du kan få en prenumerationsnyckel när du aktiverar din [kostnadsfria utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure (se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Med hjälp av valfri Java IDE skapar du ett paket.  
  
2. Skapa en fil med namnet CustomSrchJava.java i paketet och kopiera följande kod till den. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-CUSTOM-CONFIG-IDID** med din prenumerationsnyckel och ditt konfigurations-ID.  
  
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
    
    public class CustomSrchJava {       
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";  
    
        static String searchTerm = "Microsoft";  // Replace with search term specific to your search scenario.
    
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
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
                System.out.println("Invalid Custom Search subscription key!");
                System.out.println("Please paste yours into the source code.");
                System.exit(1);
            }
    
            try {
                System.out.println("Searching your slice of the Web for: " + searchTerm);
    
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
    }
    
    // Container class for search results encapsulates relevant headers and JSON data
    static class SearchResults{
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
- [Konfigurera värdbaserad UI-upplevelse](./hosted-ui.md)
- [Använda dekorationsmarkörer för att markera text](./hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
