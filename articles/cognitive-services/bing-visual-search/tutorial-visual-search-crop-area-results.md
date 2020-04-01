---
title: 'Självstudiekurs: Beskär en bild med Bing Visual Search SDK'
description: Använd Bing Visual Search SDK för att få insikter från specifika ares på en bild.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.openlocfilehash: 4778a4089c7374c1ac6a9312064dcfb1e0325b63
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478494"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Självstudiekurs: Beskär en bild med Bing Visual Search SDK för C #

Med Bing Visual Search SDK kan du beskära en bild innan du hittar liknande onlinebilder. Det här programmet beskär en enskild person från en bild som innehåller flera personer och returnerar sedan sökresultat som innehåller liknande bilder som finns på nätet.

Den fullständiga källkoden för det här programmet är tillgänglig med ytterligare felhantering och anteckningar på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Den här självstudien illustrerar hur du:

> [!div class="checklist"]
> * Skicka en begäran med Bing Visual Search SDK
> * Beskära ett bildområde för att söka med Bing Visual Search
> * Ta emot och hantera svaret
> * Hitta url:erna för åtgärdsobjekt i svaret

## <a name="prerequisites"></a>Krav

* Alla utgåvor av [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
* [NuGet-paketet för anpassad sökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installerat.
    - Högerklicka på projektet på Solution Explorer i Visual Studio och välj **Hantera NuGet-paket** på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-paketet. När du installerar NuGet-paketet för anpassad sökning installeras även följande sammansättningar:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Ange bildgrödningsområdet

Denna ansökan beskär ett område av denna bild av Microsofts ledningsgrupp. Detta beskärningsområde definieras med hjälp av övre vänstra och nedre högra koordinater, representeras som en procentandel av hela bilden:  

![Microsofts chefsteam](./media/MS_SrLeaders.jpg)

Den här bilden beskärs genom att skapa `ImageInfo` ett `ImageInfo` objekt `VisualSearchRequest`från beskärningsområdet och objektet läses in i en . Objektet `ImageInfo` innehåller också webbadressen till bilden:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Sök efter bilder som liknar beskärningsområdet

Variabeln `VisualSearchRequest` innehåller information om bildens beskärningsområde och dess URL. Metoden `VisualSearchMethodAsync()` får resultaten:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Hämta URL-data från`ImageModuleAction`

Bing visuella `ImageTag` sökresultat är objekt. Varje tagg innehåller en lista med `ImageAction`-objekt. Var `ImageAction` och `Data` en innehåller ett fält, som är en lista med värden som är beroende av typen av åtgärd.

Du kan skriva ut de olika typerna med följande kod:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Hela programmet returnerar:

|ActionType  |URL  | |
|---------|---------|---------|
|SidorInkludera WebSearchURL     |         |
|MerStorlekar WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelateradeSökningar WebSearchURL     |         |
|Entitet -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=21&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|ÄmneResultat -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResultat -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Som visas ovan `Entity` innehåller ActionType en Bing-sökfråga som returnerar information om en igenkännbar person, plats eller sak. `TopicResults`- och `ImageResults`-typerna innehåller frågor om relaterade bilder. URL:erna i listan länkar till Bings sökresultat.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Hämta webbadresser `PagesIncluding` `ActionType` för bilder

Att hämta de faktiska bild-URL:erna kräver en omvandling som läser en `ActionType` som `ImageModuleAction`, vilken innehåller ett `Data`-element med en lista med värden. Varje värde är URL:en till en bild. Följande kastar `PagesIncluding` åtgärdstypen `ImageModuleAction` till och läser värdena:

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
> [Skapa en ensidig visuell sökwebbapp](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Se även
> [Vad är API:et för visuell sökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
