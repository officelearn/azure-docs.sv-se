---
title: 'Snabbstart: Skapa en visuell sökfråga, Java – Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Så här laddar du upp en bild till API för visuell sökning i Bing och får tillbaka information om bilden.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 0e2f4d83904240cb32d3e162c4c873f0c8e469fd
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885263"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-java"></a>Snabbstart: Din första fråga till Visuell sökning i Bing i Java

API:n för visuell sökning i Bing returnerar information om en bild som du anger. Du kan ange bilden med hjälp av dess URL, en insiktstoken eller genom att ladda upp en bild. Information om alternativen finns i [Vad är API för visuell sökning i Bing?](../overview.md) Den här artikeln visar hur du laddar upp en bild. Att ladda upp en bild kan vara användbart i mobila scenarier, där du kan ta en bild av ett välkänt landmärke och få tillbaka information om det. Informationen kan exempelvis vara fakta om landmärket. 

Om du laddar upp en lokal bild måste du inkludera de formulärdata som visas nedan i brödtexten i POST. Formulärdatan måste innehålla huvudet för innehållsdispositionen. Parametern `name` måste anges till ”image” och parametern `filename` kan anges till valfri sträng. Innehållet i formuläret är binärt för bilden. Den maximala bildstorlek som du kan ladda upp är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Artikeln innehåller ett enkelt konsolprogram som skickar en begäran till API för visuell sökning i Bing och visar JSON-sökresultatet. Även om det här programmet är skrivet i Java, är API:n en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 


## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [JDK 7 eller 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) för att kompilera och köra den här koden. Du kan använda en Java IDE om du föredrar det, men ett redigeringsprogram fungerar också bra.

I snabbstarten kan du använda en prenumerationsnyckel för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) eller en betald prenumerationsnyckel.

## <a name="running-the-application"></a>Köra programmet

Nedan visas hur du laddar upp bilden med hjälp av MultipartEntityBuilder i Java.

Följ dessa steg om du vill köra programmet:

1. Hämta eller installera [gson-biblioteket](https://github.com/google/gson). Du kan också hämta det via Maven.
2. Skapa ett nytt Java-projekt i din favorit-IDE eller i ett redigeringsprogram.
3. Lägg till den angivna koden i en fil med namnet `VisualSearch.java`.
4. Ersätt värdet `subscriptionKey` med din prenumerationsnyckel.
4. Ersätt värdet `imagePath` med sökvägen till den bild som ska laddas upp.
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

[Få insikter om en bild med hjälp av en insiktstoken](../use-insights-token.md)  
[Självstudie om bilduppladdning i Visuell sökning i Bing](../tutorial-visual-search-image-upload.md)
[Självstudie om ensidesapplikationer i Visuell sökning i Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Översikt för Visuell sökning i Bing](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Skaffa en åtkomstnyckel för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referens till API för visuell sökning i Bing](https://aka.ms/bingvisualsearchreferencedoc)

