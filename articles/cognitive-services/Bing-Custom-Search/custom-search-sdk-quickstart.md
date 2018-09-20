---
title: Anpassad sökning SDK C# Snabbstart | Microsoft Docs
titleSuffix: Cognitive Services
description: Konfigurera anpassade Search SDK C#-konsolprogram.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 6b41dfbde0c2af776ee2c35220f731e40de334a0
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367856"
---
# <a name="custom-search-sdk-c-quickstart"></a>Snabbstart för SDK för anpassad sökning i C#

Bing Custom Search SDK innehåller funktionen för REST-API för entitetssökning och tolka resultaten.

Källkoden för det här exemplet är tillgänglig från [Github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).
## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med Bing Custom Search SDK, bläddra till den `Manage NuGet Packages` alternativet från Solution Explorer i Visual Studio. Lägg till paketet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

Installera den [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) paketet installerar också beroenden, inklusive följande sammansättningar:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Entiteten Search-klienten

Om du vill skapa en instans av CustomSearchAPI-klienten, lägger du till med hjälp av direktiv:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Skapa en instans av klienten för anpassad sökning: Ersätt `YOUR-CUSTOM-SEARCH-KEY` och `YOUR-CUSTOM-CONFIG-ID` med din åtkomstnyckel och konfigurationen av API-slutpunkten ID tilldelas på [Mina instanser](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Du kan använda klienten för att söka med en frågetext:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Tolka resultaten

Den `SearchAsync` metoden returnerar en `WebData` objekt som innehåller `WebPages` om någon sådan hittas för frågan. Den här koden söker efter det första resultatet och hämtar dess `Name` och `URL`.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

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
## <a name="complete-console-application"></a>Slutföra konsolprogram

Följande kod söker på frågan ”Xbox” och skriver ut `Name` och `URL` för första Webbresultat.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

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
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>Nästa steg

[Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
