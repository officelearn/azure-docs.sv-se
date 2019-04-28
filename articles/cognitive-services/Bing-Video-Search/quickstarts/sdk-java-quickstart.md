---
title: 'Snabbstart: Söka efter videor med hjälp av SDK för videosökning i Bing för Java'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att skicka sökbegäranden om video med hjälp av SDK för videosökning i Bing för Java.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: d421abf0a3cc402cf8d040363dfe79b07b66bf95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473305"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Snabbstart: Utföra en videosökning med SDK för videosökning i Bing för Java

Använd den här snabbstarten till att börja söka efter nyheter med SDK för videosökning i Bing för Java. Även om Videosökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så är SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), som innehåller fler kommentarer och funktioner för Videosökning i Bing.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Gson-biblioteket](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

Installera SDK för videosökning i Bing med hjälp av Maven eller Gradle eller något annat beroendehanteringssystem. Maven POM-filen kräver följande deklaration:

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt


Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.

    ```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
    ```

## <a name="create-a-search-client"></a>Skapa en sökklient

1. Implementera klienten `VideoSearchAPIImpl`, som kräver din API-slutpunkt och en instans av klassen `ServiceClientCredentials`.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Du implementerar `ServiceClientCredentials` genom att följa dessa steg:

    1. åsidosätt funktionen `applyCredentialsFilter()` med ett `OkHttpClient.Builder`-objekt som parameter. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. I `applyCredentialsFilter()` anropar du `builder.addNetworkInterceptor()`. Skapa ett nytt `Interceptor`-objekt och åsidosätt dess `intercept()`-metod för att ta emot ett `Chain`-avbrottsobjekt.

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. Skapa variabler för din begäran i funktionen `intercept`. Använd `Request.Builder()` till att bygga din begäran. Lägg till prenumerationsnyckeln i `Ocp-Apim-Subscription-Key`-rubriken och returnera `chain.proceed()` i begärandeobjektet.
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```

## <a name="send-a-search-request-and-receive-the-response"></a>Skicka en sökbegäran och ta emot svaret 

1. Skapa en funktion som kallas `VideoSearch()` och som tar din prenumerationsnyckel som en sträng. Instansiera den sökklient som skapades tidigare.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. I `VideoSearch()` skickar du en videosökbegäran med hjälp av klienten, med `SwiftKey` som sökterm. Om API för videosökning returnerade ett resultat hämtar du det första resultatet och skriver ut dess ID, namn och URL samt det totala antalet videor som returneras. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. Anropa sökmetoden från main-metoden.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Se också 

* [Vad är API för videosökning i Bing?](../overview.md)
* [Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)