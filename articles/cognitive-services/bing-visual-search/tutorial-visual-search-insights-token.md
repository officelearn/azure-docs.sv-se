---
title: Hitta liknande bilder från tidigare sökningar med ImageInsightsToken - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Använd SDK för visuell sökning i Bing för att hämta webbadresser till bilder som anges av ImageInsightsToken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: d005800ed317ff21389f18e4440858ea11042e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370085"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Självstudiekurs: Hitta liknande bilder från tidigare sökningar med hjälp av en bildinsiktstoken

Med Visual Search SDK kan du hitta bilder online `ImageInsightsToken`från tidigare sökningar som returnerar en . Det här `ImageInsightsToken` programmet hämtar en och använder token i en efterföljande sökning. Den skickar `ImageInsightsToken` sedan till Bing och returnerar resultat som innehåller Bing Search webbadresser och webbadresser för liknande bilder som finns online.

Den fullständiga källkoden för den här självstudien kan hittas med ytterligare felhantering och anteckningar på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Krav

* Alla utgåvor av [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Om du använder Linux/MacOS kan du köra det här programmet med [Mono](https://www.mono-project.com/).
* NuGet Visual Search och Bildsökning paket.
    - Högerklicka på projektet på Solution Explorer i Visual Studio och välj **Hantera NuGet-paket** på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch` paketet och `Microsoft.Azure.CognitiveServices.Search.ImageSearch` paketet. Installering av NuGet-paketet installerar även följande:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Hämta ImageInsightsToken från Bing Image Search SDK

Det här `ImageInsightsToken` programmet använder en som erhållits via [Bing Image Search SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Skapa en klient som anropar API:et `ImageSearchClient()`i ett nytt C#-konsolprogram med . Använd `SearchAsync()` sedan med din fråga:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Lagra det första `imageResults.Value.First()`sökresultatet med och lagra `ImageInsightsToken`sedan bildinsiktens .

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Detta `ImageInsightsToken` skickas till Bing Visual Search i en begäran.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Lägga till ImageInsightsToken i en begäran om visuell sökning

Ange `ImageInsightsToken` för en begäran om `ImageInfo` visuell sökning `ImageInsightsToken` genom att skapa ett objekt från de som finns i svar från Bing Visual Search.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Använd Bing Visual Search för att hitta bilder från en ImageInsightsToken

Objektet `VisualSearchRequest` innehåller information om bilden `ImageInfo` i som ska genomsökas. `VisualSearchMethodAsync()`-metoden hämtar resultatet. Du behöver inte ange en bild binär, eftersom bilden representeras av token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterera genom visuella sökresultat

Resultaten från Visuell sökning är `ImageTag`-objekt. Varje tagg innehåller en lista med `ImageAction`-objekt. Var `ImageAction` och `Data` en innehåller ett fält, som är en lista med värden som är beroende av typen av åtgärd. Du kan iterera `ImageTag` genom `visualSearchResults.Tags`objekten i till `ImageAction` exempel och få taggen i den. I exemplet nedan skrivs `PagesIncluding` uppgifter om åtgärder:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>SidorInkludera ActionTypes

För att hämta de faktiska bildadresserna från `ActionType` `ImageModuleAction`åtgärdstyper krävs `Data` en cast som läser en som , som innehåller ett element med en lista med värden. Varje värde är URL:en till en bild.  Följande kastar `PagesIncluding` åtgärdstypen `ImageModuleAction` till och läser värdena:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Mer information om dessa datatyper finns i [Bilder – Visuell sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Returnerade webbadresser

Det fullständiga programmet returnerar följande webbadresser:

|ActionType  |URL  | |
|---------|---------|---------|
|MerStorlekar -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|ÄmneResultat -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResultat -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Som visas ovan `TopicResults` `ImageResults` innehåller och typerna frågor för relaterade bilder. Webbadresserna länkar till Bing-sökresultat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ensidig visuell sökwebbapp](tutorial-bing-visual-search-single-page-app.md)
