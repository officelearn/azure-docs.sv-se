---
title: 'Självstudie: Bildbeskärningsområde och resultat – API för visuell sökning i Bing'
description: Använd SDK:n för Visuell sökning i Bing till att hämta URL:er för bilder som liknar beskärningsområdet i en uppladdad bild.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 27141c014c9ccdf9d62c9bde5c96bd31abfc025e
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447103"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Självstudie: SDK för visuell sökning i Bing, bildbeskärningsområde och resultat
SDK för visuell sökning innehåller ett alternativ för att välja ett område i en bild och hitta bilder online som liknar beskärningsområdet i den större bilden.  Det här exemplet anger ett beskärningsområde som visar en person från en bild som innehåller flera personer.  Koden skickar beskärningsområdet och URL:en för den större bilden och returnerar resultat som innehåller URL:er för Bing-sökning och URL:er till liknande bilder som finns online.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Visual Studio 2017](https://www.visualstudio.com/downloads/) för att köra den här koden på Windows. (Den kostnadsfria Community Edition fungerar.)

För den här självstudien behöver du starta en prenumeration på S9-prisnivån enligt [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Så här startar du en prenumeration på Azure-portalen:
1. Ange ”BingSearchV7” i den textruta längst upp på Azure-portalen där det står `Search resources, services, and docs`.  
2. Under Marketplace i den nedrullningsbara listan väljer du `Bing Search v7`.
3. Ange `Name` för den nya resursen.
4. Välj `Pay-As-You-Go`-prenumeration.
5. Välj prisnivån `S9`.
6. Starta prenumerationen genom att klicka på `Enable`.

## <a name="application-dependencies"></a>Programberoenden
Om du vill konfigurera ett konsolprogram med hjälp av SDK:n för Webbsökning i Bing, bläddrar du till alternativet Hantera NuGet Packages från Solution Explorer i Visual Studio. Add the Microsoft.Azure.CognitiveServices.Search.VisualSearch package.

När du installerar SDK-paketet för NuGet-webbsökning installeras även beroenden, inklusive:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Bild och beskärningsområde
Följande bild visar Microsofts chefsteam.  Med hjälp av SDK för visuell sökning överför vi ett beskärningsområde av bilden och hittar andra bilder och webbplatser som innehåller entiteten i det markerade området i den större bilden.  I det här fallet är entiteten en person.

![Microsofts chefsteam](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Ange beskärningsområdet som ImageInfo i VisualSearchRequest
Det här exemplet använder ett beskärningsområde i föregående bild som anger övre vänster och nedre höger koordinater som en procentandel av hela bilden.  Följande kod skapar ett `ImageInfo`-objekt från beskärningsområdet och läser in `ImageInfo`-objektet till en `VisualSearchRequest`.  `ImageInfo`-objektet innehåller även URL:en till bilden online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Söka efter bilder som liknar beskärningsområdet
`VisualSearchRequest` innehåller beskärningsområdesinformation om bilden och dess URL.  `VisualSearchMethodAsync`-metoden hämtar resultatet.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Hämta URL-data från ImageModuleAction
Resultaten från Visuell sökning är `ImageTag`-objekt.  Varje tagg innehåller en lista med `ImageAction`-objekt.  Varje `ImageAction` innehåller ett `Data`-fält, vilket är en lista med värden som beror på åtgärdstypen:

Du kan hämta de olika typerna med följande kod:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Hela programmet returnerar:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

Enligt det som visas i föregående lista innehåller `Entity` `ActionType` en Bing-sökningsfråga som returnerar information om en identifierbar person, plats eller sak.  `TopicResults`- och `ImageResults`-typerna innehåller frågor om relaterade bilder. URL:erna i listan länkar till Bings sökresultat.


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

## <a name="complete-code"></a>Fullständig kod

Följande kod kör föregående exempel. Den skickar en binär bildfil i brödtexten i POST-begäran, tillsammans med ett cropArea-objekt och skriver ut Bing-söknings-URL:er för varje ActionType. Om ActionType är PagesIncluding hämtar koden ImageObject-objektet i ImageObject Data.  Data innehåller en lista med värden som är URL:er till bilder på webbsidor.  Kopiera och klistra in de URL:er som Visuell sökning returnerade i webbläsaren om du vill se resultatet. Kopiera och klistra in ContentUrl-objekt i webbläsaren om du vill se bilderna.

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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