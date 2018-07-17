---
title: Snabbstart för Java för API för Bing Visual Search | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du överför en bild till Bing Visual Search-API och få tillbaka insikter om avbildningen.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 41e0855b126ca6e54d0a487a88fe59a0be6f72f6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072003"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>Din första fråga Bing Visual Search i Java

Bing Visual Search API returnerar information om en avbildning som du anger. Du kan ange avbildningen med hjälp av URL till bild, ett insights token, eller genom att överföra en avbildning. Information om alternativen finns i [vad är Bing Visual Search API?](../overview.md) Den här artikeln visar att ladda upp en avbildning. Ladda upp en avbildning kan vara användbart i mobila scenarier där du kan ta en bild av en välkänd landmärken och få tillbaka information om den. Insikterna kan exempelvis omfatta kunskap om landmärken. 

Om du laddar upp en lokal avbildning visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla Content-Disposition-huvudet. Dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till valfri sträng. Innehållet i formuläret är den binära filen på avbildningen. Maximal avbildningens storlek kan du överföra är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Den här artikeln innehåller ett enkelt konsolprogram som skickar en begäran om Bing Visual Search-API och visar JSON-sökresultat. Även om det här programmet är skriven i Java, är API: et en RESTful-webb-tjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 


## <a name="prerequisites"></a>Förutsättningar

Du behöver [JDK 7 eller 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) att kompilera och köra den här koden. Du kan använda en Java-IDE om du har en favorit, men en textredigerare räcker.

Den här snabbstarten kan du använda en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) prenumerationsnyckel eller en betald prenumeration-nyckel.

## <a name="running-the-application"></a>Köra programmet

Nedan visas hur du laddar upp avbildningen med hjälp av MultipartEntityBuilder i Java.

Följ dessa steg om du vill köra det här programmet:

1. Hämta eller installera den [gson biblioteket](https://github.com/google/gson). Du kan också hämta den via Maven.
2. Skapa ett nytt Java-projekt i din favorit-IDE eller redigerare.
3. Lägg till den angivna koden i en fil med namnet `VisualSearch.java`.
4. Ersätt den `subscriptionKey` värdet med din prenumerationsnyckel.
4. Ersätt den `imagePath` värdet med sökvägen för att ladda upp avbildningen.
5. Kör programmet.


```java
package uploadimage;

import java.util.*;
import java.io.*;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class UploadImage2 {

    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere";
    static String imagePath = "<pathtoyourimagetouploadgoeshere>";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addBinaryBody("image", new File(imagePath))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    
}
```

## <a name="next-steps"></a>Nästa steg

[Få insyn om en avbildning med hjälp av en token för insikter](../use-insights-token.md)  
[Bing Visual Search bild uppladdning självstudien](../tutorial-visual-search-image-upload.md)
[Bing Visual Search-självstudiekursen som ensidesapp](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search-översikt](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Hämta en kostnadsfri utvärderingsversion åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API-referens](https://aka.ms/bingvisualsearchreferencedoc)

