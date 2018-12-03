---
title: 'Snabbstart: SDK för stavningskontroll i Bing, C#'
titlesuffix: Azure Cognitive Services
description: Installation för konsolprogram med stavningskontrolls-SDK
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.openlocfilehash: 3050bc06c30c29efe7ba5294cbbee2aea1a6055b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311610"
---
# <a name="quickstart-bing-spell-check-sdk-with-c"></a>Snabbstart: SDK för stavningskontroll i Bing med C#

SDK för Stavningskontroll i Bing innehåller funktionerna för REST API för stavningskontroll.

## <a name="application-dependencies"></a>Programberoenden
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under **Sök**.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Om du vill konfigurera ett konsolprogram med hjälp av SDK för Stavningskontroll i Bing bläddrar du till alternativet `Manage NuGet Packages` från Solution Explorer i Visual Studio. Lägg till paketet `Microsoft.Azure.CognitiveServices.Language.SpellCheck`.

När du installerar [SpellCheck SDK-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck/1.2.0) installeras även beroenden, inklusive:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="spell-check-client"></a>Stavningskontrollklient

För att skapa en instans av `SpellCheckClient`-klienten lägger du till med hjälp av direktiv:

```cs
using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
```

Instansiera sedan klienten:

```cs
var client = new SpellCheckClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```

Använd klienten för att kontrollera stavning:

```cs
var result = client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof", acceptLanguage: "en-US").Result;
Console.WriteLine("Correction for Query# \"bill gatas\"");
```

Parsa in resultaten:

```cs
// SpellCheck Results
if (result?.Body.FlaggedTokens?.Count > 0)
{
    // find the first spellcheck result
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();

    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);

        var suggestions = firstspellCheckResult.Suggestions;
        if (suggestions?.Count > 0)
        {
            var firstSuggestion = suggestions.FirstOrDefault();
            Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
            Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
        }
        }
        else
        {
            Console.WriteLine("Couldn't get any Spell check results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any SpellCheck results..");
    }
```

## <a name="complete-console-application"></a>Slutföra konsolprogram

Följande konsolprogram kör den tidigare koden:

```cs
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.SpellCheck;

namespace SpellCheckSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var result = client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof", acceptLanguage: "en-US").Result;
                Console.WriteLine("Correction for Query# \"bill gatas\"");

                // SpellCheck Results
                if (result?.Body.FlaggedTokens?.Count > 0)
                {
                    // find the first spellcheck result
                    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();

                    if (firstspellCheckResult != null)
                    {
                        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
                        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
                        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
                        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);

                        var suggestions = firstspellCheckResult.Suggestions;
                        if (suggestions?.Count > 0)
                        {
                            var firstSuggestion = suggestions.FirstOrDefault();
                            Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                            Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't get any Spell check results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any SpellCheck results..");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            SpellCheckError("YOUR-ACCESS-KEY");

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

        // This will trigger an error response from the API.
        public static void SpellCheckError(string subscriptionKey)
        {
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                var result = client.SpellCheckerAsync(text: "", mode: "proof").Result;
            }
            catch (Exception ex)
            {
                Console.WriteLine("Correction for Query# \"empty text field\"");

                if (ex.GetBaseException().GetType() == typeof(Exception) )
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }
            }
        }
    }
}

```

## <a name="next-steps"></a>Nästa steg

[Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
