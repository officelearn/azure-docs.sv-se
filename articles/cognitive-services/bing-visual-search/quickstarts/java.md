---
title: 'Snabbstart: Få bildinsikter med REST API och Java - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild till API:et för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: fe323fc27062ad1bee9abdfaf3408430e28523a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446618"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Snabbstart: Få bildinsikter med hjälp av Bing Visual Search REST API och Java

Använd den här snabbstarten för att ringa ditt första samtal till API:et för visuell sökning i Bing och visa resultaten. Detta Java-program överför en avbildning till API:et och visar den information den returnerar. Även om det här programmet är skrivet i Java är API:et en RESTful Web-tjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Krav

* [Java Development Kit (JDK) 7 eller 8](https://aka.ms/azure-jdks)
* [Gson Java-bibliotek](https://github.com/google/gson)
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

2. Skapa variabler för din API-slutpunkt, prenumerationsnyckel och sökvägen till din bild. `endpoint`kan vara den globala slutpunkten nedan eller den [anpassade underdomänslutpunkten](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure-portalen för din resurs:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
    När du laddar upp en lokal bild `Content-Disposition` måste formulärdata innehålla sidhuvudet. Du måste `name` ange parametern till "image", `filename` och du kan ställa in parametern på valfri sträng. Innehållet i formuläret innehåller den binära data i bilden. Den maximala bildstorleken du kan ladda upp är 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>Skapa JSON-parsern

Skapa en metod för att göra JSON-svaret `JsonParser`från API:et mer läsbart med:

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>Konstruera sökbegäran och fråga

1. Skapa en HTTP-klient med hjälp `HttpClientBuilder.create().build();`av:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Skapa `HttpEntity` ett objekt för att överföra avbildningen till API:et:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Skapa `httpPost` ett objekt med slutpunkten och ange att sidhuvudet ska använda prenumerationsnyckeln:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Ta emot och bearbeta JSON-svaret

1. Använd `HttpClient.execute()` metoden för att skicka en begäran till API:et och lagra svaret i ett `InputStream` objekt:
    
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
> [Skapa en ensidig visuell sökapp](../tutorial-bing-visual-search-single-page-app.md)
