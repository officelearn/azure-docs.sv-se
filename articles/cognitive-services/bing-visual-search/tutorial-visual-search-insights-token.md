---
title: Hitta liknande bilder från tidigare sökningar med ImageInsightsToken-Visuell sökning i Bing
titleSuffix: Azure Cognitive Services
description: 'Använd Visuell sökning i Bing SDK för att hämta URL: er för avbildningar som anges av ImageInsightsToken.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: dff96b19f40c2d897b6a018a4c46cec60f8aa201
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689314"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Hitta liknande bilder från tidigare sökningar med ImageInsightsToken

Med Visuell sökning SDK kan du hitta bilder online från tidigare sökningar som returnerar ett `ImageInsightsToken`. Det här programmet hämtar ett `ImageInsightsToken` och använder token i en efterföljande sökning. Den skickar sedan `ImageInsightsToken` till Bing och returnerar resultat som inkluderar Bing-sökning webb adresser och URL: er för liknande bilder som hittades online.

Den fullständiga käll koden för den här själv studie kursen hittar du ytterligare fel hantering och anteckningar på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Krav

* En version av [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Om du använder Linux/MacOS kan du köra det här programmet med [mono](https://www.mono-project.com/).
* NuGet-Visuell sökning och Bildsökning-paket.
    - Från Solution Explorer i Visual Studio högerklickar du på projektet och väljer **Hantera NuGet-paket** på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-paketet och `Microsoft.Azure.CognitiveServices.Search.ImageSearch`-paketet. Installering av NuGet-paketet installerar även följande:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Hämta ImageInsightsToken från Bildsökning i Bing SDK

Det här programmet använder en `ImageInsightsToken` som erhållits via [bildsökning I Bing SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). I ett nytt C# konsol program skapar du en-klient för att anropa API: et med hjälp av `ImageSearchClient()`. Använd sedan `SearchAsync()` med din fråga:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Lagra det första Sök resultatet med `imageResults.Value.First()`och lagra sedan bild insiktens `ImageInsightsToken`.

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

Den här `ImageInsightsToken` skickas till Visuell sökning i Bing i en begäran.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Lägg till ImageInsightsToken i en Visuell sökning begäran

Ange `ImageInsightsToken` för en Visuell sökning begäran genom att skapa ett `ImageInfo`-objekt från `ImageInsightsToken` som finns i svar från Visuell sökning i Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Använda Visuell sökning i Bing för att hitta bilder från en ImageInsightsToken

`VisualSearchRequest`-objektet innehåller information om avbildningen i `ImageInfo` som ska genomsökas. `VisualSearchMethodAsync()`-metoden hämtar resultatet. Du behöver inte ange en binär avbildning eftersom bilden representeras av token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterera genom Visuell sökning resultat

Resultaten från Visuell sökning är `ImageTag`-objekt. Varje tagg innehåller en lista med `ImageAction`-objekt. Varje `ImageAction` innehåller ett `Data` fält, vilket är en lista över värden som är beroende av typen av åtgärd. Du kan iterera genom `ImageTag` objekt i `visualSearchResults.Tags`, till exempel och hämta `ImageAction`-taggen i den. Exemplet nedan skriver ut information om `PagesIncluding` åtgärder:

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

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Hämtning av faktiska bild-URL: er från åtgärds typer kräver en Cast som läser en `ActionType` som `ImageModuleAction`, som innehåller ett `Data`-element med en lista med värden. Varje värde är URL:en till en bild.  Följande skickar åtgärds typen `PagesIncluding` till `ImageModuleAction` och läser värdena:

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

## <a name="returned-urls"></a>Returnerade URL: er

Det fullständiga programmet returnerar följande URL: er:

|Åtgärds  |URL  | |
|---------|---------|---------|
|MoreSizes-> WebSearchUrl     |         |
|VisualSearch-> WebSearchUrl     |         |
|ImageById-> WebSearchUrl    |         |
|RelatedSearches-> WebSearchUrl:    |         |
|DocumentLevelSuggestions-> WebSearchUrl:     |         |
|TopicResults-> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults-> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Som du ser ovan innehåller `TopicResults` och `ImageResults`s typer frågor för relaterade bilder. URL: en länkar till Bing search-resultat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en Visuell sökning webb program med en enda sida](tutorial-bing-visual-search-single-page-app.md)
