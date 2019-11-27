---
title: 'Snabb start: Hämta bild insikter med hjälp av REST API och Java-Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar du upp en bild till API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: eecca2372c7265d456276a966cc441b15c17272a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383609"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Snabb start: Hämta bild insikter med hjälp av Visuell sökning i Bing REST API och Java

Använd den här snabb starten för att göra ditt första anrop till API för visuell sökning i Bing och visa resultatet. Java-programmet överför en avbildning till API: et och visar den information som returneras. Även om det här programmet är skrivet i Java är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

När du laddar upp en lokal avbildning måste formulär data innehålla `Content-Disposition` rubriken. Du måste ange dess `name` parameter till "bild" och du kan ange parametern `filename` till valfri sträng. Innehållet i formuläret är en bilds binära data. Den maximala bild storlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Krav

* [Java Development Kit (JDK) 7 eller 8](https://aka.ms/azure-jdks)
* [Gson Java-biblioteket](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa ett nytt Java-projekt i din favorit-IDE eller-redigerare och importera följande bibliotek:

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

2. Skapa variabler för API-slutpunkten, prenumerations nyckeln och sökvägen till din avbildning:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Skapa JSON-parsern

Skapa en metod för att göra JSON-svaret från API: et mer läsbart med `JsonParser`:

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruera sökbegäran och fråga

1. I appens huvud metod skapar du en HTTP-klient med hjälp av `HttpClientBuilder.create().build();`:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Skapa ett `HttpEntity`-objekt för att ladda upp avbildningen till API: et:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Skapa ett `httpPost`-objekt med din slut punkt och ange att rubriken ska använda din prenumerations nyckel:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Ta emot och bearbeta JSON-svaret

1. Använd metoden `HttpClient.execute()` om du vill skicka en begäran till API: et och lagra svaret i ett `InputStream`-objekt:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Lagra JSON-strängen och skriv ut svaret:

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bygg en Visuell sökning webb program med en enda sida](../tutorial-bing-visual-search-single-page-app.md)
