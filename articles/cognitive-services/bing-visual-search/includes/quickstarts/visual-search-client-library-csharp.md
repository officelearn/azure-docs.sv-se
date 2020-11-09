---
title: Snabb start för Visuell sökning i Bing C#-klient bibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c50d13f35f3d4f04cacba736e7e9fd5f3fba6f5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371576"
---
Använd den här snabb starten för att börja hämta Image Insights från tjänsten Visuell sökning i Bing, med hjälp av C#-klient biblioteket. Även om Visuell sökning i Bing har en REST API som är kompatibel med de flesta programmeringsspråk, är klient biblioteket ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

[Referens dokumentation](/dotnet/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-dotnet)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VisualSearch/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)

## <a name="prerequisites"></a>Förutsättningar

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
* NuGet-paketet för Visuell sökning. 
    - Från Solution Explorer i Visual Studio högerklickar du på projektet och väljer `Manage NuGet Packages` på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.VisualSearch`-paketet. Installering av NuGet-paketet installerar även följande:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt projekt i Visual Studio. Lägg sedan till följande direktiv.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Skapa instans av klienten med prenumerationsnyckeln.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Skicka en sökbegäran 

1. Skapa en `FileStream` till bilderna (i det här fallet `TestImages/image.jpg`). Sedan använder du klienten för att skicka en sökbegäran med `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Parsa resultaten från den föregående frågan:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../../tutorial-bing-visual-search-single-page-app.md)