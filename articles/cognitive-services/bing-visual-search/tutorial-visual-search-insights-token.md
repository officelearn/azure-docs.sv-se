---
title: Bing Visual Search SDK ImageInsightsToken självstudien | Microsoft Docs
description: 'Så här använder Bing Visual Search SDK för att hämta URL: er för bilder som anges av ImageInsightsToken.'
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 8f6e7f7e88ae78fe7e8a9be4adefd689dd26d0f9
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "41987540"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Självstudie: Bing Visual Search SDK ImageInsightsToken och resultat
Visual Search SDK innehåller ett alternativ för att hitta avbildningar online från en tidigare sökning som returnerar en `ImageInsightsToken`.  Det här exemplet hämtar en `ImageInsightsToken` och använder token i en efterföljande sökning.  Koden skickar den `ImageInsightsToken` till Bing och returnerar resultat som innehåller URL: er för Bing-sökning och URL: er av liknande bilder som finns online.

## <a name="prerequisites"></a>Förutsättningar
Visual Studio 2017. Om behövs kan du hämta kostnadsfria community-versionen härifrån: https://www.visualstudio.com/vs/community/.
En cognitive services API-nyckel krävs för att autentisera SDK-anrop. Registrera dig för en kostnadsfri utvärderingsversion nyckel. Nyckeln utvärderingsversion är bra för sju dagar med ett anrop per sekund. Köp en åtkomstnyckel för produktionsscenarier. Se även information om priser.
Möjligheten att köra .NET core SDK, .net core 1.1-appar. Du kan ta CORE, ramverk och Runtime härifrån: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Programberoenden
Om du vill konfigurera ett konsolprogram med Bing Web Search SDK, bläddrar du till alternativet hantera NuGet-paket från Solution Explorer i Visual Studio. Lägg till:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage paket.

Installera NuGet Web Search SDK-paketet installerar även beroenden, inklusive:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Hämta ImageInsightsToken från bildsökning
Det här exemplet används en `ImageInsightsToken` erhålls av följande metod.  Läs mer om det här anropet [bild Search SDK C# Snabbstart](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Koden söker efter resultat på en fråga för 'Kanadensiska Rockies ”och hämtar en ImageInsightsToken. Den skriver ut den första bilden insights token, miniatyr-url och innehåll bild-url.  Metoden returnerar den `ImageInsightsToken`för användning i en efterföljande Visual Search-begäran.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Ange ImageInsightsToken för Visual Search begäran
Det här exemplet används insights-token som returneras av den tidigare metoden. Följande kod skapar ett `ImageInfo` objekt från den `ImageInsightsToken` och läser in objektet ImageInfo i en `VisualSearchRequest`. Ange `ImageInsightsToken` i en `ImageInfo` för den `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Använd Visual Search för att hitta bilder från en ImageInsightsToken
Den `VisualSearchRequest` innehåller information om avbildningen som ska söka efter i den `ImageInfo` objekt.  Den `VisualSearchMethodAsync` metoden hämtar resultaten.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Hämta URL-data från ImageModuleAction
Visuella sökresultat `ImageTag` objekt.  Varje tagg innehåller en lista över `ImageAction` objekt.  Varje `ImageAction` innehåller en `Data` fält som är en lista med värden som beror på vilken typ av åtgärd:

Du kan få de olika typerna med följande kod:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Hela appen returnerar:

* Åtgärdstyp: MoreSizes WebSearchUrl ->:
* Åtgärdstyp: VisualSearch -> WebSearchUrl:
* Åtgärdstyp: ImageById -> WebSearchUrl:
* Åtgärdstyp: RelatedSearches -> WebSearchUrl:
* Åtgärdstyp: DocumentLevelSuggestions -> WebSearchUrl:
* Åtgärdstyp: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1
* Åtgärdstyp: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1

Enligt föregående lista de `TopicResults` och `ImageResults` typer innehåller frågor om relaterade bilder. URL: er i listlänk till Bing-sökresultat.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>URL: er i PagesIncluding ActionType av avbildningar som upptäckts av Visual Search

De faktiska bild-URL: er kräver en typkonvertering som läser en `ActionType` som `ImageModuleAction`, som innehåller en `Data` element med en lista med värden.  Varje värde är Webbadressen till en bild.  Följande sändningar den `PagesIncluding` åtgärdstyp till `ImageModuleAction` och läser värdena.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Mer information om dessa datatyper finns i [avbildningar - Visual Search](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Fullständiga koden

Följande kod körs föregående exempel. Den skickar den `ImageInsightsToken` i en post-begäran. Sedan den skriver ut Bing Sök URL: er för varje åtgärdstypen. Om åtgärdstypen är `PagesIncluding`, koden hämtar den `ImageObject` objekt i `Data`.  Den `Data` innehåller en lista med värden för URL: er för bilder på webbsidor.  Kopiera och klistra in resulterande Visual Search URL: er till webbläsare för att visa resultat. Kopiera och klistra in ContentUrl objekt till webbläsare om du vill visa bilder.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

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

                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }

                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```
## <a name="next-steps"></a>Nästa steg
[Visual Search-svar](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
