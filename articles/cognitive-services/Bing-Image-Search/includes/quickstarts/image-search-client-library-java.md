---
title: Bing Image Search Java-klientbibliotek snabbstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 4656d8116ddd2e618adad97aeea576e88bc8785f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78899582"
---
Använd den här snabbstarten för att göra din första bildsökning med hjälp av Bing Image Search-klientbiblioteket, som är ett omslag för API:et och innehåller samma funktioner. Den här enkla Java-appen skickar en bildsökningsfråga, parsar JSON-svaret och visar webbadressen till den första bild som returneras.

Källkoden för det här exemplet är tillgänglig [på GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) med ytterligare felhantering och anteckningar.

## <a name="prerequisites"></a>Krav

Den senaste versionen av [Java Development Kit (JDK)](https://aka.ms/azure-jdks)

Installera klientbiblioteksberoendena för Bing Image Search med Hjälp av Maven, Gradle eller ett annat beroendehanteringssystem. Maven POM-filen kräver följande deklaration:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt Java-projekt i valfri IDE eller valfritt redigeringsprogram och lägg till följande importer i din klassimplementering:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. Skapa variabler i huvudmetoden för din prenumerationsnyckel och sökvillkor. Sedan skapa en instans av bildsökningsklienten i Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Skicka en sökbegäran till API:et

1. Med hjälp av `bingImages().search()`, skicka en HTTP-begäran som innehåller sökfrågan. Spara svaret som en `ImagesModel`.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Parsa och visa resultatet

Parsa bild resultatet som returneras i svaret.
Om svaret innehåller sökresultat ska du lagra och skriva ut information om det första resultatet, såsom en webbadress till miniatyrbilden, en webbadress till originalet samt det totala antalet returnerade bilder.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie om enkel app för bildsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Se även

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en interaktiv demo online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Hämta en kostnadsfri åtkomstnyckel för Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Java-exempel för Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [API-referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
