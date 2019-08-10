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
ms.date: 06/18/2019
ms.author: rosh
ms.openlocfilehash: 5f4faa290fe4ed02ab1ed75d23755af5dc20f215
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880612"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Hitta liknande bilder från tidigare sökningar med ImageInsightsToken

Med Visuell sökning SDK kan du hitta bilder online från tidigare sökningar som returnerar en `ImageInsightsToken`. Det här programmet hämtar `ImageInsightsToken` ett och använder token i en efterföljande sökning. Den skickar `ImageInsightsToken` sedan till Bing och returnerar resultat som omfattar Bing-sökning-URL: er och URL: er för liknande bilder som hittas online.

Den fullständiga käll koden för den här själv studie kursen hittar du ytterligare fel hantering och anteckningar på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Förutsättningar

* En version av [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Om du använder Linux/MacOS kan du köra det här programmet med [mono](https://www.mono-project.com/).
* NuGet-Visuell sökning och Bildsökning-paket.
    - Från Solution Explorer i Visual Studio högerklickar du på projektet och väljer **Hantera NuGet-paket** på menyn. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` Installera paketet`Microsoft.Azure.CognitiveServices.Search.ImageSearch` och paketet. Installering av NuGet-paketet installerar även följande:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Hämta ImageInsightsToken från Bildsökning i Bing SDK

Det här programmet använder `ImageInsightsToken` en som hämtats via [bildsökning i Bing SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). I ett nytt C# konsol program skapar du en-klient för att anropa API `ImageSearchClient()`: et med hjälp av. Använd `SearchAsync()` sedan med frågan:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Lagra det första Sök resultatet med `imageResults.Value.First()`och lagra sedan bild `ImageInsightsToken`insikterna.

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

Detta `ImageInsightsToken` skickas till visuell sökning i Bing i en begäran.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Lägg till ImageInsightsToken i en Visuell sökning begäran

Ange en visuell sökning begäran genom att skapa ett `ImageInfo` objekt från det `ImageInsightsToken` som finns i svar från visuell sökning i Bing. `ImageInsightsToken`

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Använda Visuell sökning i Bing för att hitta bilder från en ImageInsightsToken

Objektet innehåller information om bilden i `ImageInfo` som ska genomsökas. `VisualSearchRequest` `VisualSearchMethodAsync()`-metoden hämtar resultatet. Du behöver inte ange en binär avbildning eftersom bilden representeras av token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterera genom Visuell sökning resultat

Resultaten från Visuell sökning är `ImageTag`-objekt. Varje tagg innehåller en lista med `ImageAction`-objekt. Varje `ImageAction` innehåller ett `Data` fält, som är en lista över värden som är beroende av typen av åtgärd. Du kan iterera igenom `ImageTag` objekten i `visualSearchResults.Tags`, till `ImageAction` exempel och hämta taggen i den. Exemplet nedan skriver ut information om `PagesIncluding` åtgärder:

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

Hämtning av faktiska bild-URL: er från åtgärds typer kräver en `ActionType` Cast `ImageModuleAction`som läser en as `Data` -åtgärd som innehåller ett-element med en lista med värden. Varje värde är URL:en till en bild.  Följande `PagesIncluding` skickar åtgärds typen till `ImageModuleAction` och läser värdena:

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

|Åtgärdstyp  |URL  | |
|---------|---------|---------|
|MoreSizes-> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches-> WebSearchUrl:    |         |
|DocumentLevelSuggestions-> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.Bing.com/CR?ig=3E32CC6CA5934FBBA14ABC3B2E4651F9&cid=1BA795A21EAF6A63175699B71FC36B7C&RD=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3A%2F%2Fwww.Bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.Bing.com/CR?ig=3E32CC6CA5934FBBA14ABC3B2E4651F9&cid=1BA795A21EAF6A63175699B71FC36B7C&RD=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3A%2F%2Fwww.Bing.com%2fimages%2fsearch%3Fq%3doutdoor&p=DevEx,5831.1       |

Som du `TopicResults` ser ovan innehåller typerna `ImageResults` och olika frågor för relaterade bilder. URL: en länkar till Bing search-resultat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en Visuell sökning webb program med en enda sida](tutorial-bing-visual-search-single-page-app.md)
