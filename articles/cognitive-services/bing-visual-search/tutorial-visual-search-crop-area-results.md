---
title: 'Självstudier: Beskär en bild med Bing Visual Search SDK'
description: Använd Bing Visual Search SDK för att få insikter från specifika ar på en bild.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/03/2019
ms.author: rosh
ms.openlocfilehash: a6a98d9ddf76e86feb35c6d3d71f7777b0edd51d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491142"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Självstudier: Beskär en bild med Bing Visual Search SDK förC#

Bing Visual Search SDK kan du beskär en bild innan att söka efter liknande online bilder. Det här programmet Beskär en enskild person från en avbildning som innehåller flera personer och returnerar sedan sökresultat som innehåller liknande bilder som finns online.

Den fullständiga källkoden för det här programmet är tillgängligt med ytterligare felhantering och kommentarer på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Den här självstudien visar hur du:

> [!div class="checklist"]
> * Skicka en begäran med hjälp av Bing Visual Search SDK
> * Beskära ett område i bild att söka med Bing Visual Search
> * Ta emot och hantera svaret
> * Hitta URL: er med åtgärdsobjekt i svaret

## <a name="prerequisites"></a>Nödvändiga komponenter

* Valfri version av [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
* [NuGet-paketet för anpassad sökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installerat.
    - Solution Explorer i Visual Studio högerklickar du på projektet och välj **hantera NuGet-paket** på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-paketet. När du installerar NuGet-paketet för anpassad sökning installeras även följande sammansättningar:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Ange området Beskär bilden

Det här programmet Beskär en del av den här avbildningen av Microsofts team för personer i ledningen. Det här beskärningsområdet definieras med hjälp av längst upp till vänster och höger koordinater, representeras som en procentandel av hela avbildningen:  

![Microsofts chefsteam](./media/MS_SrLeaders.jpg)

Den här bilden beskärs genom att skapa en `ImageInfo` objekt från beskärningsområdet och läser in den `ImageInfo` objekt i en `VisualSearchRequest`. Den `ImageInfo` objekt innehåller även URL: en för bilden:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Sök efter bilder som liknar beskärningsområdet

Variabeln `VisualSearchRequest` innehåller information om bildens Beskär område och URL: en. Den `VisualSearchMethodAsync()` metoden hämtar resultaten:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Hämta data från URL `ImageModuleAction`

Bing Visual Search-resultaten `ImageTag` objekt. Varje tagg innehåller en lista med `ImageAction`-objekt. Varje `ImageAction` innehåller en `Data` fältet, som är en lista med värden som beror på vilken typ av åtgärd.

Du kan skriva ut de olika typerna med följande kod:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Hela programmet returnerar:

|Åtgärdstyp  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Enligt ovan, den `Entity` ActionType innehåller en Bing search-fråga som returnerar information om en identifierbara person, plats eller sak. `TopicResults`- och `ImageResults`-typerna innehåller frågor om relaterade bilder. URL:erna i listan länkar till Bings sökresultat.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Hämta URL: er för `PagesIncluding` `ActionType` bilder

Att hämta de faktiska bild-URL:erna kräver en omvandling som läser en `ActionType` som `ImageModuleAction`, vilken innehåller ett `Data`-element med en lista med värden. Varje värde är URL:en till en bild. Följande sändningar den `PagesIncluding` åtgärdstyp till `ImageModuleAction` och läser värdena:

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
> [Skapa en enda sida i Visual Search webbapp](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Se också
> [Vad är API:et för visuell sökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
