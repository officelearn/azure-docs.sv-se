---
title: 'Snabbstart: Utföra en nyhetssökning – SDK för nyhetssökning i Bing för Java'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att söka efter nyheter med SDK för nyhetssökning i Bing för Java och bearbeta svaret.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 5bfd07eb11fd4504fa8c674ecb144c5fe328cc06
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149585"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Snabbstart: Söka efter nyheter med SDK för nyhetssökning i Bing för Java

Använd den här snabbstarten om du vill börja söka efter nyheter med SDK för nyhetssökning i Bing för Java. Även om Nyhetssökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Nödvändiga komponenter

Installera beroenden för SDK för nyhetssökning i Bing med hjälp av Maven, Gradle eller något annat beroendehanteringssystem. Maven POM-filen kräver följande deklaration:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Skapa en sökklient och lagra autentiseringsuppgifter

1. Skapa en metod med namnet `getClient()` som returnerar en ny `NewsSearchAPIImpl`-sökklienten. Lägg till slutpunkten som den första parametern för det nya `NewsSearchAPIImpl`-objektet och ett nytt `ServiceClientCredentials`-objekt för att lagra autentiseringsuppgifterna.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Skapa `ServiceClientCredentials`-objektet genom att åsidosätta funktionen `applyCredentialsFilter()`. Skicka en `OkHttpClient.Builder`-metod och använd verktygets metod `addNetworkInterceptor()` för att skapa autentiseringsuppgifterna för SDK-anropet.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran

1. Skapa en metod som anropar `getClient()` och skickar en sökbegäran till tjänsten Nyhetssökning i Bing. Filtrera sökningen med parametrarna *market* och *count*. Skriv sedan ut information om det första nyhetsresultatet: namn, URL, publiceringsdatum, beskrivning, providernamn samt totalt antal uppskattade matchningar för sökningen.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Kör koden genom att lägga till sökmetoden i en `main()`-metod.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
[Skapa en enkelsidig webbapp](tutorial-bing-news-search-single-page-app.md)
