---
title: 'Snabbstart: Hämta information om bilder med hjälp av REST API för visuell sökning i Bing och Java'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild till API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 2fe4e9dad0b198fe54e06ce07100d231f1f7d157
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046452"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Snabbstart: Hämta information om bilder med hjälp av REST API för visuell sökning i Bing och Java

Använd den här snabbstarten för att skapa ditt första anrop till Bing Visual Search API och granska resultaten. Det här Java-programmet laddar upp en bild-API: et och visar den information som returneras. Även om det här programmet är skriven i Java, är API: et en RESTful-webb-tjänst som är kompatibla med de flesta programmeringsspråk.

När du laddar upp en lokal avbildning formulärdata måste innehålla den `Content-Disposition` rubrik. Måste du ställa in dess `name` parametern till ”bild” och du kan ange den `filename` parametern till valfri sträng. Innehållet i formuläret inkludera binära data för avbildningen. Den maximala bildstorlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Förutsättningar

* Den [Java Development Kit (JDK) 7 eller 8](https://aka.ms/azure-jdks)
* Den [Gson Java-bibliotek](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa ett nytt Java-projekt i din favorit-IDE eller redigerare och importera följande bibliotek:

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

2. Skapa variabler för din API-slutpunkt, prenumerationsnyckel och sökvägen till din avbildning:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Skapa JSON-parsern

Skapa en metod för att se JSON-svar från API: et mer läsbara med `JsonParser`:

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruera sökbegäran och fråga

1. I den huvudsakliga metoden för ditt program skapar du en HTTP-klient som använder `HttpClientBuilder.create().build();`:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Skapa en `HttpEntity` objekt för att överföra avbildningen till API: et:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Skapa en `httpPost` objekt med din slutpunkt och ange huvudet för att använda din prenumerationsnyckel:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Ta emot och bearbeta JSON-svaret

1. Använd den `HttpClient.execute()` metod för att skicka en begäran till API: et och lagra svaren i en `InputStream` objekt:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Store JSON-sträng och skrivas ut:

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enda sida i Visual Search webbapp](../tutorial-bing-visual-search-single-page-app.md)
