---
title: 'Snabbstart: Sök efter bilder med API för bildsökning i Bing och Java'
description: Använd den här snabbstarten till att göra din första bildsökning med API för bildsökning i Bing, som är en adapterklass för API:et och innehåller samma funktioner. Den här enkla Java-appen skickar en bildsökningsfråga, parsar JSON-svaret och visar webbadressen till den första bild som returneras.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 81bd7356579b3e4f7b82497bb2163c85374fd0d9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316335"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-java"></a>Snabbstart: Sök efter bilder med API för bildsökning i Bing och Java

Använd den här snabbstarten till att göra din första bildsökning med API för bildsökning i Bing, som är en adapterklass för API:et och innehåller samma funktioner. Den här enkla Java-appen skickar en bildsökningsfråga, parsar JSON-svaret och visar webbadressen till den första bild som returneras.

Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) tillsammans med ytterligare felhantering och kommentarer.

## <a name="prerequisites"></a>Nödvändiga komponenter
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under **Sök**.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Den senaste versionen av [Java Development Kit (JDK)](https://aka.ms/azure-jdks)

Installera SDK för bildsökning i Bing med hjälp av Maven eller Gradle eller något annat beroendehanteringssystem. Maven POM-filen kräver följande deklaration:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och lägga till följande importer i din klassimplementering:

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

## <a name="send-a-search-request-to-the-bing-image-search-api"></a>Skicka en sökbegäran till API för bildsökning i Bing

1. Med hjälp av `bingImages().search()`, skicka en HTTP-begäran som innehåller sökfrågan. Spara svaret som en `ImagesModel`.
   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Parsa och visa resultatet

Parsa bild resultatet som returneras i svaret.
Om svaret innehåller sökresultat ska du lagra och skriva ut information om det första resultatet, som en webbadress till miniatyrbilden, en webbadress till originalet och det totala antalet returnerade bilder.  

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
* [API-referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
