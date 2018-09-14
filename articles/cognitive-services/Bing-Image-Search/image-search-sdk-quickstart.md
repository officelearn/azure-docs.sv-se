---
title: 'Snabbstart: Sök efter bilder med hjälp av SDK för Bing Search-avbildningen och C#'
description: Använd den här snabbstarten för att söka efter bilder på webben med hjälp av SDK för Bing Search-avbildningen och C#.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 9e0decb29224b5ad684e1242b8f93e091e08e6b6
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579260"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-c"></a>Snabbstart: Sök efter bilder med SDK för Bing Search-avbildningen och C#

Använd den här snabbstarten för att göra sökningen första avbildning med hjälp av Bing-bild Search SDK, som är en Omslutning för API: et och innehåller samma funktioner. Det här enkla C#-programmet skickar en sökfråga för avbildningen, Parsar JSON-svar och visar Webbadressen till den första bilden som returneras.

Källkoden för det här exemplet finns [på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) med ytterligare felhantering och anteckningar.

## <a name="prerequisites"></a>Förutsättningar

* En utgåva av [Visual Studio 2017](https://visualstudio.microsoft.com/vs/whatsnew/).
* Den [Cognitive bild Search NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0).

Om du vill installera Search SDK för Bing-bild i visual studio, den `Manage NuGet Packages` alternativet från Solution Explorer i Visual Studio.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera programmet

Skapa först ett nytt C#-konsolprogram i Visual Studio. Lägg sedan till följande paket i projektet.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

Skapa variabler för prenumerationsnyckeln giltig bild resultatet som returneras av Bing och en sökterm i ditt projekt main-metoden. Sedan skapa en instans av avbildningen search klienten med hjälp av nyckeln.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";
//initialize the client
var client = new ImageSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Skicka en sökfråga med hjälp av klienten

Du kan använda klienten för att söka med en frågetext:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Parsa och visa det första resultatet för avbildning

Parsa bild resultatet som returneras i svaret.
Om svaret innehåller sökresultat, lagra det första resultatet och skriva ut information om, till exempel en miniatyrbild URL, den ursprungliga URL: en, tillsammans med det totala antalet returnerade avbildningar.  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bildsökning i Bing ensidesapp självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Se också 

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en online Interaktiv demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Hämta en kostnadsfri Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [.NET-exempel för Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)