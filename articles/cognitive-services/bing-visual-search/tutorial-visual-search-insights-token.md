---
title: Hitta liknande bilder från tidigare sökningar med hjälp av image Insights-token och API för visuell sökning i Bing
titleSuffix: Azure Cognitive Services
description: 'Använd Visuell sökning i Bing klient bibliotek för att hämta URL: er för bilder från tidigare sökningar.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: f68618c4387bc5105914eccc4e6149650119fd0f
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366085"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Självstudie: hitta liknande bilder från tidigare sökningar med en image Insights-token

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Med Visuell sökning klient biblioteket kan du hitta bilder online från tidigare sökningar som returnerar en `ImageInsightsToken` . Det här programmet hämtar ett `ImageInsightsToken` och använder token i en efterföljande sökning. Den skickar sedan `ImageInsightsToken` till Bing och returnerar resultat som omfattar Bing-sökning-URL: er och URL: er för liknande bilder som hittas online.

Den fullständiga käll koden för den här själv studie kursen hittar du ytterligare fel hantering och anteckningar på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Förutsättningar

* En version av [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Om du använder Linux/MacOS kan du köra det här programmet med [mono](https://www.mono-project.com/).
* NuGet-Visuell sökning och Bildsökning-paket.
    - Från Solution Explorer i Visual Studio högerklickar du på projektet och väljer **Hantera NuGet-paket** på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch` paketet och `Microsoft.Azure.CognitiveServices.Search.ImageSearch` paketet. Installering av NuGet-paketet installerar även följande:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>Hämta ImageInsightsToken från klient biblioteket för Bildsökning i Bing

Det här programmet använder en `ImageInsightsToken` hämtade genom [bildsökning i Bing klient biblioteket](../bing-image-search/quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp). I ett nytt C#-konsol program skapar du en-klient för att anropa API: et med `ImageSearchClient()` . Använd sedan `SearchAsync()` med frågan:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Lagra det första Sök resultatet med `imageResults.Value.First()` och lagra sedan bild insikterna `ImageInsightsToken` .

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

Ange `ImageInsightsToken` en visuell sökning begäran genom att skapa ett `ImageInfo` objekt från det som `ImageInsightsToken` finns i svar från visuell sökning i Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Använda Visuell sökning i Bing för att hitta bilder från en ImageInsightsToken

`VisualSearchRequest`Objektet innehåller information om bilden i `ImageInfo` som ska genomsökas. `VisualSearchMethodAsync()`-metoden hämtar resultatet. Du behöver inte ange en binär avbildning eftersom bilden representeras av token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterera genom Visuell sökning resultat

Resultaten från Visuell sökning är `ImageTag`-objekt. Varje tagg innehåller en lista med `ImageAction`-objekt. Varje `ImageAction` innehåller ett `Data` fält, som är en lista över värden som är beroende av typen av åtgärd. Du kan iterera igenom `ImageTag` objekten i `visualSearchResults.Tags` , till exempel och hämta `ImageAction` taggen i den. Exemplet nedan skriver ut information om `PagesIncluding` åtgärder:

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

Hämtning av faktiska bild-URL: er från åtgärds typer kräver en Cast som läser en `ActionType` as-åtgärd som `ImageModuleAction` innehåller ett- `Data` element med en lista med värden. Varje värde är URL:en till en bild.  Följande skickar `PagesIncluding` Åtgärds typen till `ImageModuleAction` och läser värdena:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Mer information om dessa datatyper finns i [Bilder – Visuell sökning](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Returnerade URL: er

Det fullständiga programmet returnerar följande URL: er:

|ActionType  |URL  |
|---------|---------|
|MoreSizes-> WebSearchUrl     |         |
|VisualSearch-> WebSearchUrl     |         |
|ImageById-> WebSearchUrl    |         |
|RelatedSearches-> WebSearchUrl:    |         |
|DocumentLevelSuggestions-> WebSearchUrl:     |         |
|TopicResults-> WebSearchUrl    | https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID = 1BA795A21EAF6A63175699B71FC36B7C&RD = 1&h = BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fdiscover% 2fcanadian% 2brocky&p = DevEx, 5823.1       |
|ImageResults-> WebSearchUrl    |  https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID = 1BA795A21EAF6A63175699B71FC36B7C&RD = 1&h = PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fimages% 2fsearch% 3Fq% 3doutdoor&p = DevEx, 5831.1       |

Som du ser ovan `TopicResults` `ImageResults` innehåller typerna och olika frågor för relaterade bilder. URL: en länkar till Bing search-resultat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en Visuell sökning webb program med en enda sida](tutorial-bing-visual-search-single-page-app.md)