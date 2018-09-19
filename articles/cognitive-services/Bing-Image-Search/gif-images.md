---
title: Hämta .gif bilder - bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Hur du använder Bings API för bildsökning för att få mer information om .gif-bilder.
services: cognitive-services
author: MikeDodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 30a266a1dc0441485b4f6ba70876ac32c3de0e14
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296488"
---
# <a name="search-for-gif-images-using-the-bing-image-search-api"></a>Sök efter .gif bilder med hjälp av den bildsökning i Bing

Sökning i Bing kan du också söka i hela webb för de mest relevanta .gif-avbildningarna.  Utvecklare kan integrera engagerande GIF-filer i olika scenarier för konversationen. 

Följande URL är en fråga för animerad GIF-avbildningar.
````
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
````
Den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) parametern anger sökvillkor.  Den föregående frågan anger också `animatedGif` med hjälp av den [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) filter-parametern.

Om du vill se exempel på resultat att använda följande URL för att söka bing.com.
````
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

````
## <a name="query-parameters"></a>Frågeparametrar

Mer information om frågeparametrar och alternativ finns i den [bild Search API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Ett exempel som visas nedan under rubriken [exempel Sök efter animerad gif med hjälp av Java](#gifExample).

## <a name="tips-and-suggestions"></a>Användbara tips och råd

- Du kan ange [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) och [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) parametrar. Vi rekommenderar att maxFileSize = 2000000 eftersom merparten av GIF-filer i vårt index är under 2MB.  Detta hjälper också till att kontrollera storleken på data när bandbredd är viktig, t.ex i mobila mobila scenarier.
- Du kan förbättra upplevd prestanda genom att läsa in miniatyrbilden först före inläsning av käll-url.  
- För första gången eller startsida annons där du inte har en användarfråga ännu, försök att använda våra populära gif-sökningar för att från den [populära bilder API](trending-images.md).
- Det finns tre inställningar för den [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) parametern.  Den `strict` alternativet blockerar vuxet innehåll.
- Se [mkt](supported-countries-markets.md) fullständig lista över språk och platser som stöds.
- *AnimatedGifHttps* endast returnerar animerade GIF-bilder från en https-adress. Många program kräver anslutning till externa länkar för säkerhet, över https. Till exempel kräver Apple App Store anslutning till webbtjänster via HTTPS, som krypterar användardata säker under överföringen.

<a name="gifExample" />
## <a name="example-search-for-animated-gif-using-java"></a>Exempel Sök efter animerad gif med hjälp av Java

Följande URL söker efter animerade GIF-bilder: `q=interesting`
````
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

````
I följande exempel visas URL-frågan kräver [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers) rubrik.

Java-exemplet nedan skapar och skickar en begäran.

````
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
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

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

````

## <a name="results"></a>Resultat
Koden får följande resultat som JSON-objekt:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](quickstarts/csharp.md)
- [Självstudie för bildsökning ensidesprogram](tutorial-bing-image-search-single-page-app.md)
