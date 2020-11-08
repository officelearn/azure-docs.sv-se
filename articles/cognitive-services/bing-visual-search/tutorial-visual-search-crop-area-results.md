---
title: 'Självstudie: Beskär en bild med Visuell sökning i Bing SDK'
description: Använd Visuell sökning i Bing SDK för att få insikter från vissa ar på en avbildning.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 14b32e4d666a81b8cba0e23c99b5878b1d9d4351
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368074"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Självstudie: Beskär en avbildning med Visuell sökning i Bing SDK för C #

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Med Visuell sökning i Bing SDK kan du beskära en avbildning innan du hittar liknande online-avbildningar. Det här programmet Beskär en enskild person från en avbildning som innehåller flera personer och returnerar sedan Sök resultaten som innehåller liknande avbildningar som finns online.

Den fullständiga käll koden för det här programmet är tillgänglig med ytterligare fel hantering och anteckningar på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Den här självstudien visar hur du:

> [!div class="checklist"]
> * Skicka en begäran med hjälp av Visuell sökning i Bing SDK
> * Beskär ett bild område för att söka med Visuell sökning i Bing
> * Ta emot och hantera svaret
> * Hitta URL: er för åtgärds objekt i svaret

## <a name="prerequisites"></a>Förutsättningar

* En version av [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
* [NuGet-paketet för anpassad sökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installerat.
    - Från Solution Explorer i Visual Studio högerklickar du på projektet och väljer **Hantera NuGet-paket** på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-paketet. När du installerar NuGet-paketet för anpassad sökning installeras även följande sammansättningar:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Ange bild beskärnings ytan

Det här programmet beskär ett del av den här avbildningen av Microsoft Senior ledarskaps grupp. Beskärnings området definieras med hjälp av övre vänstra och nedre högra koordinater som visas som en procent andel av hela bilden:  

![Microsofts chefsteam](./media/MS_SrLeaders.jpg)

Den här bilden beskärs genom att skapa ett `ImageInfo` objekt från beskärnings ytan och läsa in `ImageInfo` objektet i en `VisualSearchRequest` . `ImageInfo`Objektet innehåller även bildens URL:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Sök efter bilder som liknar beskärnings ytan

Variabeln `VisualSearchRequest` innehåller information om bildens beskärnings områden och dess URL. `VisualSearchMethodAsync()`Metoden hämtar resultatet:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Hämta URL-data från `ImageModuleAction`

Visuell sökning i Bing resultat är `ImageTag` objekt. Varje tagg innehåller en lista med `ImageAction`-objekt. Varje `ImageAction` innehåller ett `Data` fält, som är en lista över värden som är beroende av typen av åtgärd.

Du kan skriva ut de olika typerna med följande kod:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Hela programmet returnerar:

|ActionType  |URL  |
|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entitet-> WebSearchUrl     | https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&RD = 1&h = BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fsearch% 3Fq% 3dSatya% 2bNadella&p = DevEx, 5380.1        |
|TopicResults-> WebSearchUrl    |  https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&RD = 1&h = 3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fdiscover% 2fnadella% 2bsatya&p = DevEx, 5382.1        |
|ImageResults-> WebSearchUrl    |  https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&RD = 1&h = l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fimages% 2fsearch% 3Fq% 3dSatya% 2bNadella&p = DevEx, 5384.1        |

Som du ser ovan `Entity` innehåller åtgärds en Bing-Sök fråga som returnerar information om en identifierbar person, plats eller sak. `TopicResults`- och `ImageResults`-typerna innehåller frågor om relaterade bilder. URL:erna i listan länkar till Bings sökresultat.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Hämta URL: er för `PagesIncluding` `ActionType` bilder

Att hämta de faktiska bild-URL:erna kräver en omvandling som läser en `ActionType` som `ImageModuleAction`, vilken innehåller ett `Data`-element med en lista med värden. Varje värde är URL:en till en bild. Följande skickar `PagesIncluding` Åtgärds typen till `ImageModuleAction` och läser värdena:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Skapa en Visuell sökning webb program med en enda sida](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Se även
> [Vad är API:et för visuell sökning i Bing?](./overview.md)