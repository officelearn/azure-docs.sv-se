---
title: 'Snabbstart: SDK för anpassad sökning, C#'
titleSuffix: Azure Cognitive Services
description: Konfigurera C#-konsolprogrammet för SDK:t för anpassad sökning.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 5abf1027059bed9c685e0eb44f17ab41dfabf655
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816777"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>Snabbstart: Använda SDK:t för anpassad sökning i Bing med C#

SDK:t för anpassad sökning i Bing tillhandahåller en enklare programmeringsmodell än REST-API:et för anpassad sökning i Bing. Det här avsnittet beskriver hur du gör dina första anrop med anpassad sökning med hjälp av SDK:t för C#.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

- En anpassad sökinstans som är redo att användas. Se sidan om att [skapa din första instans för anpassad sökning i Bing](quick-start.md).  
  
- En prenumerationsnyckel. Du kan få en prenumerationsnyckel när du aktiverar din [kostnadsfria utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure (se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Visual Studio 2017 installerat. Om du inte redan har det kan du hämta **kostnadsfria** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- [NuGet-paketet för anpassad sökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installerat. Från Solution Explorer i Visual Studio högerklickar du på projektet och väljer `Manage NuGet Packages` på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-paketet.

När du installerar NuGet-paketet för anpassad sökning installeras även följande sammansättningar:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Öppna Visual Studio 2017.
  
2. Klicka på **Arkiv**-menyn, klicka på **Nytt**, **Projekt** och sedan på mallen för **Visual C#-konsolprogrammet**.
  
3. Ge lösningen namnet CustomSearchSolution och bläddra till mappen för att placera lösningen i den.
  
4. Klicka på OK för att skapa lösningen.  
  
4. Från Solution Explorer högerklickar du på projektet (det har samma namn som lösningen) och väljer `Manage NuGet Packages`. Klicka på **Bläddra** i NuGet-pakethanteraren. Ange Microsoft.Azure.CognitiveServices.Search.CustomSearch i sökrutan och tryck på Retur. Välj paketet och klicka på Installera.  
  
4. Kopiera följande kod till filen Program.cs. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-CUSTOM-CONFIG-IDID** med din prenumerationsnyckel och ditt konfigurations-ID.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

                try
                {
                    // This will look up a single query (Xbox).
                    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                    Console.WriteLine("Searched for Query# \" Xbox \"");

                    //WebPages
                    if (webData?.WebPages?.Value?.Count > 0)
                    {
                        // find the first web page
                        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                        if (firstWebPagesResult != null)
                        {
                            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
                            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find web results!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Didn't see any Web data..");
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. Skapa och kör (felsök) lösningen. 




## <a name="breaking-it-down"></a>Analysera delarna

När du har installerat NuGet-paketet för anpassad sökning måste du lägga till ett using-direktiv för det.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Skapa en instans av klienten för anpassad sökning, som du använder för att göra sökförfrågningar. Ersätt `YOUR-SUBSCRIPTION-KEY` med din nyckel.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Nu kan du använda klienten för att skicka en sökförfrågan. Ersätt `YOUR-CUSTOM-CONFIG-ID` med instansens konfigurations-ID (du hittar ID:t på [portalen för anpassad sökning](https://www.customsearch.ai/)). Det här exemplet söker efter Xbox. Uppdatera efter behov.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

`SearchAsync`-metoden returnerar ett `WebData`-objekt. Använd `WebData` för att iterera genom eventuella `WebPages` som hittades. Den här koden hittar det första webbsidesresultatet och skriver ut webbsidans `Name` och `URL`.

```csharp
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```


## <a name="next-steps"></a>Nästa steg

Utforska SDK-exemplen. Varje exempel innehåller en Viktigt-fil med information om nödvändiga komponenter och hur exemplen installeras och körs.

* Kom igång med [.NET-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Mer information om definitioner och beroenden finns i [.NET-bibliotek](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch).
* Kom igång med [NodeJS-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Mer information om definitioner och beroenden finns i [NodeJS-bibliotek](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch).
* Kom igång med [Java-exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Mer information om definitioner och beroenden finns i [Java-bibliotek](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch).
* Kom igång med [Python-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Mer information om definitioner och beroenden finns i [Python-bibliotek](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch).

