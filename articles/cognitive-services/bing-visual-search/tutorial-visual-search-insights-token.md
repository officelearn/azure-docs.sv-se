---
title: 'Självstudie: ImageInsightsToken – Visuell sökning i Bing'
titlesuffix: Azure Cognitive Services
description: Använd SDK:n för Visuell sökning i Bing till att hämta URL:er för bilder som anges av ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 62780500d29c891182d3869bf0ba3ccdc5e2f715
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441069"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Självstudie: SDK för Visuell sökning i Bing, ImageInsightsToken och resultat
SDK:n för Visuell sökning innehåller ett alternativ för att hitta bilder på nätet från en tidigare sökning som returnerar en `ImageInsightsToken`.  Det här exemplet hämtar en `ImageInsightsToken` och använder token i en efterföljande sökning.  Koden skickar `ImageInsightsToken` till Bing och returnerar resultat som innehåller URL:er för Bing-sökning och URL:er till liknande bilder som finns online.

## <a name="prerequisites"></a>Nödvändiga komponenter
Visual Studio 2017. Om det behövs kan du hämta den kostnadsfria community-versionen härifrån: https://www.visualstudio.com/vs/community/.
En API-nyckel för Cognitive Services krävs för att autentisera SDK-anrop. Registrera dig för att få en nyckel till en kostnadsfri utvärderingsversion. Utvärderingsnyckeln gäller i sju dagar med ett anrop per sekund. Om du har produktionsscenarier köper du en åtkomstnyckel. Se även prisinformationen.
Möjlighet att köra .NET Core SDK, .Net Core 1.1-appar. Du kan hämta CORE, Framework och Runtime härifrån: https://www.microsoft.com/net/download/.

För den här självstudien behöver du starta en prenumeration på S9-prisnivån enligt [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Så här startar du en prenumeration på Azure-portalen:
1. Ange ”BingSearchV7” i den textruta längst upp på Azure-portalen där det står `Search resources, services, and docs`.  
2. Under Marketplace i den nedrullningsbara listan väljer du `Bing Search v7`.
3. Ange `Name` för den nya resursen.
4. Välj `Pay-As-You-Go`-prenumeration.
5. Välj prisnivån `S9`.
6. Starta prenumerationen genom att klicka på `Enable`.

## <a name="application-dependencies"></a>Programberoenden
Om du vill konfigurera ett konsolprogram med hjälp av SDK:n för Webbsökning i Bing, bläddrar du till alternativet Hantera NuGet Packages från Solution Explorer i Visual Studio. Lägg till:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage-paket.

När du installerar SDK-paketet för NuGet-webbsökning installeras även beroenden, inklusive:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Hämta ImageInsightsToken från Bildsökning

I det här exemplet används en `ImageInsightsToken` som erhålls med följande metod.  Läs mer om det här anropet i [Snabbstart för SDK för bildsökning i C#](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Koden söker efter resultat för en fråga om ”Canadian Rockies”och hämtar en ImageInsightsToken. Den skriver ut den första bildens insiktstoken, miniatyr-URL och bildinnehålls-URL.  Metoden returnerar `ImageInsightsToken` för användning i en efterföljande begäran för Visuell sökning.

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

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Ange ImageInsightsToken för Visuell sökning-begärandet

I det här exemplet används den insiktstoken som returnerades av föregående metod. Följande kod skapar ett `ImageInfo`-objekt från `ImageInsightsToken` och läser in objektet ImageInfo i en `VisualSearchRequest`. Ange `ImageInsightsToken` i en `ImageInfo` för `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Använd Visuell sökning till att hitta bilder från en ImageInsightsToken

`VisualSearchRequest` innehåller information om bilden som söks efter i `ImageInfo`-objektet.  `VisualSearchMethodAsync`-metoden hämtar resultaten.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Hämta URL-data från ImageModuleAction
Resultaten från Visuell sökning är `ImageTag`-objekt.  Varje tagg innehåller en lista med `ImageAction`-objekt.  Varje `ImageAction` innehåller ett `Data`-fält, vilket är en lista med värden som beror på åtgärdstypen:

Du kan hämta de olika typerna med följande kod:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Hela programmet returnerar:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

Enligt föregående lista innehåller `TopicResults`- och `ImageResults`-typer frågor om relaterade bilder. URL:erna i listan länkar till Bings sökresultat.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>PagesIncluding ActionType URL:er till bilder som hittats med Visuell sökning

Att hämta de faktiska bild-URL:erna kräver en omvandling som läser en `ActionType` som `ImageModuleAction`, vilken innehåller ett `Data`-element med en lista med värden.  Varje värde är URL:en till en bild.  Nedanstående omvandlar `PagesIncluding`-åtgärdstypen till `ImageModuleAction` och läser värdena.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Mer information om dessa datatyper finns i [Bilder – Visuell sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="complete-code"></a>Fullständig kod

Följande kod kör föregående exempel. Den skickar `ImageInsightsToken` i en POST-begäran. Sedan skriver den ut Bing-sökningens URL:er för varje ActionType. Om ActionType är `PagesIncluding`, hämtar koden `ImageObject`-objekt i `Data`.  `Data` innehåller en lista med värden som är URL:er till bilder på webbsidor.  Kopiera och klistra in de URL:er som Visuell sökning returnerade i webbläsaren om du vill se resultatet. Kopiera och klistra in ContentUrl-objekt i webbläsaren om du vill se bilderna.

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
[Svar från Visuell sökning](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
