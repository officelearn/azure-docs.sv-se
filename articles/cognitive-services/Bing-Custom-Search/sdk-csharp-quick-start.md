---
title: Anpassad sökning SDK C# Snabbstart | Microsoft Docs
titleSuffix: Cognitive Services
description: Konfigurera anpassade Search SDK C#-konsolprogram.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6c9917e3a63515f36b386e444edcc53de07799fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949935"
---
# <a name="c-sdk-quickstart"></a>C# SDK-Snabbstart

Bing Custom Search SDK tillhandahåller en enklare programmeringsmodell än Bing Custom Search REST API. Det här avsnittet går igenom din första Custom Search-anrop med hjälp av C#-SDK.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra den här snabbstarten:

- En färdiga att använda anpassad Sökinstans. Se [skapar din första Bing Custom Search-instans](quick-start.md).  
  
- En prenumerationsnyckel. Du kan få en prenumerationsnyckel när du aktiverar din [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), eller du kan använda en betald prenumerationsnyckel från instrumentpanelen i Azure (se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Visual Studio 2017 installerat. Om du inte redan har det kan du hämta den **kostnadsfria** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- Den [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) paketet. Solution Explorer i Visual Studio högerklickar du på projektet och välj `Manage NuGet Packages` på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-paketet.

Installera anpassad sökning i NuGet-paketet installerar även följande sammansättningar:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Öppna Visual Studio 2017.
  
2. Klicka på den **filen** menyn klickar du på **New**, **projekt**, och sedan den **Visual C#-konsolprogram** mall.
  
3. Namnge din lösning CustomSearchSolution och bläddra till mappen för att placera den i.
  
4. Klicka på OK för att skapa lösningen.  
  
4. Solution Explorer högerklickar du på ditt projekt (det har samma namn som lösningen) och välj `Manage NuGet Packages`. Klicka på **Bläddra** i NuGet-Pakethanteraren. Ange Microsoft.Azure.CognitiveServices.Search.CustomSearch i sökrutan och tryck på RETUR. Välj paketet och klicka på installera.  
  
4. Kopiera följande kod i filen Program.cs. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-anpassad-CONFIG-ID** med din prenumerationsnyckel och konfiguration-ID.  
  
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
  
5. Skapa och kör (debug) lösningen. 




## <a name="breaking-it-down"></a>Bryta ned

När du har installerat anpassad sökning i NuGet-paketet som du behöver lägga till en med hjälp av direktiv för den.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Därefter skapa en instans av anpassad sökning i klienten, som du använder för att göra sökförfrågningar. Se till att ersätta `YOUR-SUBSCRIPTION-KEY` med din nyckel.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Du kan nu använda klienten för att skicka en sökbegäran. Se till att ersätta din `YOUR-CUSTOM-CONFIG-ID` med din instans konfigurations-ID (du hittar ID i den [anpassad sökning i portalen](https://www.customsearch.ai/)). Det här exemplet söker efter Xbox. Uppdatera efter behov.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

Den `SearchAsync` metoden returnerar en `WebData` objekt. Använd `WebData` att gå igenom någon `WebPages` som hittades. Den här koden söker efter det första resultatet för webbsidan och skriver ut på webbsidan `Name` och `URL`.

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

Titta på SDK-exempel. Varje exempel innehåller en Viktigt-fil med information om nödvändiga komponenter och installation/körs exemplen.

* Kom igång med [.NET-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Se även [.NET-biblioteken](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch) för definitioner och beroenden.
* Kom igång med [NodeJS-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Se även [NodeJS bibliotek](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch) för definitioner och beroenden.
* Kom igång med [Java-exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Se även [Java-bibliotek](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch) för definitioner och beroenden.
* Kom igång med [Python-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Se även [Python-biblioteken](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch) för definitioner och beroenden.

