---
title: 'Snabbstart: Hämta information om bilder med hjälp av REST API för visuell sökning i Bing och Java'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild till API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 67cd215479efb40e3eb7e81c6c519a59b6526f47
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172042"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Snabbstart: Hämta information om bilder med hjälp av REST API för visuell sökning i Bing och Java

Använd den här snabbstarten för att skicka ditt första anrop till API för visuell sökning i Bing och visa sökresultaten. Det här enkla C#-programmet laddar upp en bild till API:et och visar den information som returneras om den. Även om det här programmet är skrivet i Java, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

När du laddar upp den lokala bilden måste formulärdata innehålla huvudet för innehållsdisposition. Parametern `name` måste anges till ”image” och parametern `filename` kan anges till valfri sträng. Innehållet i formuläret är binärt för bilden. Den maximala bildstorlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Java Development Kit(JDK) 7 eller 8](https://aka.ms/azure-jdks)
* [Gson-biblioteket](https://github.com/google/gson)
* [Apache HttpComponents](http://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Skapa variabler för din API-slutpunkt, prenumerationsnyckel och sökvägen till din bild. 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Skapa JSON-parsern

Skapa en metod för att göra JSON-svar från API:et mer läsbart med hjälp av `JsonParser`.

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruera sökbegäran och fråga

1. I main-metoden för ditt program skapar du en HTTP-klient med hjälp av `HttpClientBuilder.create().build();`.

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Skapa en `HttpEntity` för att ladda upp bilden till API:et.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Skapa ett `httpPost`-objekt med din slutpunkt och ange huvudet till att använda din prenumerationsnyckel.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Ta emot och bearbeta JSON-svaret

1. Använd `HttpClient.execute()` för att skicka en begäran till API:et, och lagra svaret i ett `InputStream`-objekt.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Lagra JSON-strängen och skriv ut svaret.

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](../tutorial-bing-visual-search-single-page-app.md)
