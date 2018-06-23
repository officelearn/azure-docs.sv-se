---
title: Java-Snabbstart för Bing Visual sökning API | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du överför en bild till Bing Visual Sök-API och få tillbaka insikter om bilden.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 8160302faa373d69b65afe6b68a8efb44442850d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355134"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>Första Bing Visual sökfrågan i Java

Bing Visual Sök API returnerar information om en avbildning som du anger. Du kan ange avbildningen med hjälp av URL-Adressen till bilden, en insikter token, eller genom att ladda upp en bild. Information om dessa alternativ finns [vad är Bing Visual Sök API?](../overview.md) Den här artikeln visar ladda upp en bild. Ladda upp en bild kan vara användbart i mobila scenarier där du kan ta en bild av en välkänd Landmärke och få tillbaka information om den. Insikter kan exempelvis omfatta kunskap om Landmärke. 

Om du överför en lokal image visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla rubriken Content-Disposition. Dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till en sträng. Innehållet i formuläret är binär för bilden. Du kan ladda upp maximala bildstorleken är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Den här artikeln innehåller ett enkelt konsolprogram som skickar en begäran i Bing Visual Sök-API och visar sökresultatet JSON. När det här programmet är skriven i Java är API: et en RESTful webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 


## <a name="prerequisites"></a>Förutsättningar

Du behöver [JDK 7 eller 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) att kompilera och köra den här koden. Du kan använda en Java-IDE om du har en favorit, men det räcker en textredigerare.

Du kan använda för Snabbstart, en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) prenumeration nyckeln eller en betald prenumeration nyckel.

## <a name="running-the-application"></a>Köra programmet

Nedan visas hur du laddar upp avbildningen med hjälp av MultipartEntityBuilder i Java.

Följ dessa steg om du vill köra det här programmet:

1. Hämtar eller installerar den [gson biblioteket](https://github.com/google/gson). Du kan även hämta den via Maven.
2. Skapa ett nytt Java-projekt i din favorit IDE eller -redigeraren.
3. Lägg till den angivna kod i en fil med namnet `VisualSearch.java`.
4. Ersätt den `subscriptionKey` värdet med din prenumeration nyckel.
4. Ersätt den `imagePath` värdet med sökvägen till bilden som ska överföras.
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

[Få insikter om en avbildning med hjälp av en insights-token](../use-insights-token.md)  
[Bing Visual Sök sida app självstudiekursen](../tutorial-bing-visual-search-single-page-app.md)  
[Översikt över Bing Visual sökning](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Hämta en kostnadsfri utvärderingsversion snabbtangent](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Sök API-referens](https://aka.ms/bingvisualsearchreferencedoc)

