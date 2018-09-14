---
title: 'Snabbstart: Sök efter bilder med hjälp av SDK för Bing Search-avbildningen och Java'
description: Använd den här snabbstarten för att söka efter bilder på webben med hjälp av SDK för Bing Search-avbildningen och Java.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 12bd6f9a9a0b43b4571a7e0311ffbea54c7b9054
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574069"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-java"></a>Snabbstart: Sök efter bilder med SDK för Bing Search-avbildningen och Java

Använd den här snabbstarten för att göra sökningen första avbildning med hjälp av Bing-bild Search SDK, som är en Omslutning för API: et och innehåller samma funktioner. Den här enkla Java-program skickar en sökfråga för avbildningen, Parsar JSON-svar och visar Webbadressen till den första bilden som returneras.

Källkoden för det här exemplet finns [på GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) med ytterligare felhantering och anteckningar. 

## <a name="prerequisites"></a>Förutsättningar 

Den senaste versionen av den [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)

Installera SDK för Bing Search-bild-beroenden med hjälp av Maven eller Gradle en annan beroendehanteringssystem. Maven POM-filen kräver följande försäkran:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
 </dependencies> 
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera programmet

1. Skapa ett nytt Java-projekt i din favorit-IDE eller redigerare och Lägg till följande importer i klassen-implimentation:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. Skapa variabler för din prenumerationsnyckel i main-metoden och Sök efter termer. Sedan skapa en instans av klienten för bildsökning i Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-bing-image-search-api"></a>Skicka en sökbegäran till sökning i Bing

1. Med hjälp av `bingImages().search()`, skicka HTTP-begäran som innehåller sökfrågan. Spara svaret som en `ImagesModel`.
    ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Tolka och visa resultatet

Parsa bild resultatet som returneras i svaret.
Om svaret innehåller sökresultat, lagra det första resultatet och skriva ut information om, till exempel en miniatyrbild URL, den ursprungliga URL: en, tillsammans med det totala antalet returnerade avbildningar.  

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
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bildsökning i Bing ensidesapp självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Se också 

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en online Interaktiv demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Hämta en kostnadsfri Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) 
* [Java-exempel för Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
