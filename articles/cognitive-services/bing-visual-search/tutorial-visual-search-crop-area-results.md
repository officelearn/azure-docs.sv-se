---
title: Bing Visual Search SDK beskärningsområdet resulterar självstudien | Microsoft Docs
description: 'Så här använder Bing Visual Search SDK för att hämta URL: er för avbildningar liknar beskära tänkbara uppladdade bilden.'
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: dd51ed7c710cc51a9fe0e63e55aa0d2c4ea24bee
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574497"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Självstudie: Bing Visual Search SDK bild Beskär område och resultat
Visual Search SDK innehåller ett alternativ att välja ett område i en bild och hitta avbildningar online som liknar beskärningsområdet större bild.  Det här exemplet anger Beskär område som visar en person från en avbildning som innehåller flera personer.  Koden skickar beskärningsområdet och större bildens URL och returnerar resultat som innehåller URL: er för Bing-sökning och URL: er av liknande bilder som finns online.

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Visual Studio 2017](https://www.visualstudio.com/downloads/) att hämta den här koden som körs på Windows. (Den kostnadsfria Community Edition fungerar.)

Du måste ha en [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med API: er för Bing-sökresultat. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) är tillräcklig för den här snabbstarten. Du behöver åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumerationsnyckel från instrumentpanelen i Azure.

## <a name="application-dependencies"></a>Programberoenden
Om du vill konfigurera ett konsolprogram med Bing Web Search SDK, bläddrar du till alternativet hantera NuGet-paket från Solution Explorer i Visual Studio. Lägg till Microsoft.Azure.CognitiveServices.Search.VisualSearch-paketet.

Installera NuGet Web Search SDK-paketet installerar även beroenden, inklusive:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Bilden och Beskär område
Följande bild visar företagsledningen för Microsoft-teamet.  Med hjälp av Visual Search SDK, vi överför en Beskär delar av bilden och hitta andra avbildningar och på webbsidor som innehåller entiteten i markerat område för större bild.  I det här fallet är entiteten en person.

![Microsoft företagsledningen-teamet](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Ange beskärningsområdet som ImageInfo i VisualSearchRequest
Det här exemplet används ett Beskär område i föregående bild som anger längst upp till vänster och sänka rätt koordinater med procent av hela bilden.  Följande kod skapar ett `ImageInfo` objekt från Beskär område och belastning på `ImageInfo` objekt i en `VisualSearchRequest`.  Den `ImageInfo` objekt innehåller också URL till bild online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Sök efter bilder som liknar beskära området
Den `VisualSearchRequest` innehåller Beskär områdesinformation om avbildningen och dess URL.  Den `VisualSearchMethodAsync` metoden hämtar resultaten.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Hämta URL-data från ImageModuleAction
Visuella sökresultat `ImageTag` objekt.  Varje tagg innehåller en lista över `ImageAction` objekt.  Varje `ImageAction` innehåller en `Data` fält som är en lista med värden som beror på vilken typ av åtgärd:

Du kan få de olika typerna med följande kod:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Hela appen returnerar:

* Åtgärdstyp: PagesIncluding WebSearchURL:
* Åtgärdstyp: MoreSizes WebSearchURL:
* Åtgärdstyp: VisualSearch WebSearchURL:
* Åtgärdstyp: ImageById WebSearchURL: 
* Åtgärdstyp: RelatedSearches WebSearchURL:
* Åtgärdstyp: Entitet -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5380.1
* Åtgärdstyp: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx, 5382.1
* Åtgärdstyp: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5384.1

Enligt föregående lista de `Entity` `ActionType` innehåller en sökning i Bing-fråga som returnerar information om en identifierbara person, plats eller sak.  Den `TopicResults` och `ImageResults` typer innehåller frågor om relaterade bilder. URL: er i listlänk till Bing-sökresultat.


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

## <a name="complete-code"></a>Fullständiga koden

Följande kod körs föregående exempel. Den skickar en bild binära i brödtexten i post-begäran, tillsammans med ett cropArea-objekt och visar Bing söka i URL: er för varje åtgärdstypen. Om åtgärdstypen är PagesIncluding, hämtar koden ImageObject objekt i ImageObject Data.  Data innehåller en lista med värden för URL: er för bilder på webbsidor.  Kopiera och klistra in resulterande Visual Search URL: er till webbläsare för att visa resultat. Kopiera och klistra in ContentUrl objekt till webbläsare om du vill visa bilder.

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
[Visual Search-svar](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)