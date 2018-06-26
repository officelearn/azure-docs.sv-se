---
title: Bing Visual Sök SDK ImageInsightsToken kursen | Microsoft Docs
description: 'Hur du använder Bing Visual Sök SDK för att hämta URL: er för bilder som anges av ImageInsightsToken.'
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 578fa90f504920030b488d2b8fa3a2d0232cccce
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753678"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Självstudier: Bing Visual Sök SDK ImageInsightsToken och resultat
Visual Sök SDK innehåller ett alternativ för att hitta avbildningar online från en tidigare sökning som returnerar en `ImageInsightsToken`.  Det här exemplet hämtar en `ImageInsightsToken` och använder token i en efterföljande sökning.  Koden skickar den `ImageInsightsToken` till Bing och returnerar resultat som innehåller URL: er för Bing Search och URL: er för liknande bilder finns online.

## <a name="prerequisites"></a>Förutsättningar
Visual Studio 2017. Om nödvändigt, kan du hämta ledigt gruppversion härifrån: https://www.visualstudio.com/vs/community/.
En kognitiva services API-nyckel krävs för att autentisera SDK-anrop. Registrera dig för en kostnadsfri utvärderingsversion nyckel. Utvärderingsversion nyckeln är bra för sju dagar med ett anrop per sekund. Köp en åtkomstnyckel för produktion scenarier. Se även information om priser.
Möjligheten att köra .NET core SDK för .net core 1.1 appar. Du får kärnor och Framework Runtime härifrån: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Programberoenden
Bläddra till alternativet hantera NuGet-paket från Solution Explorer i Visual Studio för att ställa in ett konsolprogram med Bing Web Sök SDK. Lägg till:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage paket.

Installera NuGet Web Sök SDK-paketet installerar även beroenden, inklusive:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Hämta ImageInsightsToken från Image-sökning
Det här exemplet används en `ImageInsightsToken` erhålls med följande metod.  Mer information om det här anropet finns [avbildningen Sök SDK C# quickstart](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Koden söker efter resultat på en fråga för 'Kanadensiska Rockies' och hämtar en ImageInsightsToken. Den skriver ut den första bilden insikter token, miniatyrbildens url och bild-url för innehåll.  Metoden returnerar den `ImageInsightsToken`för användning i en efterföljande Visual sökbegäran.

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

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Ange ImageInsightsToken för Visual sökbegäran
Det här exemplet använder insikter token som returneras av metoden föregående. Följande kod skapar en `ImageInfo` objekt från den `ImageInsightsToken` och läser in objektet ImageInfo i en `VisualSearchRequest`. Ange `ImageInsightsToken` i en `ImageInfo` för den `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Använda Visual sökning avbildningar från en ImageInsightsToken
Den `VisualSearchRequest` innehåller information om bilden ska söka efter i den `ImageInfo` objekt.  Den `VisualSearchMethodAsync` metoden hämtar resultaten.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Hämta URL-data från ImageModuleAction
Visual sökresultatet `ImageTag` objekt.  Varje tagg innehåller en lista över `ImageAction` objekt.  Varje `ImageAction` innehåller en `Data` fält som är en lista över värden som beror på vilken typ av åtgärd:

Du kan hämta de olika typerna med följande kod:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Hela programmet returnerar:

* Åtgärdstyp: MoreSizes -> WebSearchUrl:
* Åtgärdstyp: VisualSearch -> WebSearchUrl:
* Åtgärdstyp: ImageById -> WebSearchUrl:
* Åtgärdstyp: RelatedSearches -> WebSearchUrl:
* Åtgärdstyp: DocumentLevelSuggestions -> WebSearchUrl:
* Åtgärdstyp: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1
* Åtgärdstyp: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1

Som visas i föregående lista de `TopicResults` och `ImageResults` typer innehåller frågor om relaterade bilder. URL: er i listlänk till Bing sökresultat.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>PagesIncluding åtgärdstyp URL: er för avbildningar hittades vid sökning i Visual

Hämtar faktiska bildens URL: er kräver en omvandling som läser ett `ActionType` som `ImageModuleAction`, som innehåller en `Data` element med en lista med värden.  Varje värde är URL-Adressen till en bild.  Följande webbsändningar den `PagesIncluding` åtgärdstyp till `ImageModuleAction` och läser värdena.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Mer information om dessa datatyper finns [bilder - Visual Sök](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Fullständiga koden

Följande kod körs föregående exempel. Tjänsten skickar den `ImageInsightsToken` i en post-begäran. Sedan ut Bing söka URL: er för varje åtgärdstyp. Om åtgärdstypen `PagesIncluding`, koden hämtar den `ImageObject` objekt i `Data`.  Den `Data` innehåller en lista med värden som är URL: er för bilder på webbsidor.  Kopiera och klistra in resulterande Visual Sök URL: er till webbläsaren om du vill visa resultat. Kopiera och klistra in ContentUrl objekt till webbläsaren om du vill visa bilder.

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
[Visual Search-svar](https://review.docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview?branch=pr-en-us-44614#the-response)