---
title: 'Snabbstart: Analysera textinnehåll och leta efter stötande material i C#'
titlesuffix: Azure Cognitive Services
description: Så här analyserar du textinnehåll för olika stötande material med SDK för Content Moderator för .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: sajagtap
ms.openlocfilehash: 0540a81db93570928dd33b66a69b6883b2df0cd9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007696"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Snabbstart: Analysera textinnehåll och leta efter stötande material i C# 

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med [Content Moderator SDK för .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Du kommer lära dig att köra termbaserad filtrering och klassificering av innehåll i syfte att kontrollera potentiellt stötande material.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter
- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator och få din nyckel.
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> Den här guiden använder en kostnadsfri nivå för Content Moderator-prenumeration. Information om vad ingår i varje prenumerationsnivå finns det på sidan [Priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

1. Skapa ett nytt projekt för **Konsolprogram (.NET Framework)** i Visual Studio och ge det namnet **TextModeration**. 
1. Om det finns andra projekt i din lösning väljer du den här kopian som det enda startprojektet.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Sök efter och installera följande paket:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-text-moderation-code"></a>Lägga till kod för textmoderering

Nu ska du kopiera och klistra in koden från den här guiden i ditt projekt för att implementera ett grundläggande innehållsmodereringsscenario.

### <a name="include-namespaces"></a>Inkludera namnområden

Lägg till följande `using`-instruktioner överst i *Program.cs*-filen.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Skapa Content Moderator-klienten

Lägg till följande kod i filen *Program.cs* för att skapa en Content Moderator-klient för din prenumeration. Lägg till koden tillsammans med **programklassen** i samma namnområde. Du behöver uppdatera fälten **AzureRegion** och **CMSubscriptionKey** med värdena för din regionsidentifierare och prenumerationsnyckel.

```csharp
// Wraps the creation and configuration of a Content Moderator client.
public static class Clients
{
    // The region/location for your Content Moderator account, 
    // for example, westus.
    private static readonly string AzureRegion = "YOUR API REGION";

    // The base URL fragment for Content Moderator calls.
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    // Your Content Moderator subscription key.
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    // Returns a new Content Moderator client for your subscription.
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="set-up-input-and-output-targets"></a>Konfigurera inkommande och utgående mål

Lägg till följande statiska fält till klassen **Program** i _Program.cs_. Dessa anger filerna för ingående datatextinnehåll och utgående JSON-innehåll.

```csharp
// The name of the file that contains the text to evaluate.
private static string TextFile = "TextFile.txt";

// The name of the file to contain the output from the evaluation.
private static string OutputFile = "TextModerationOutput.txt";
```

Du måste skapa indatafilen *TextFile.txt* och uppdatera dess sökväg (relativa sökvägar är relativa för körningskatalogen). Öppna _TextFile.txt_ och lägg till text som ska modereras. Den här snabbstarten använder följande exempeltext:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Läsa in indatatexten

Lägg till följande kod i metoden **Main**. Metoden **ScreenText** är en viktig åtgärd. Dess parametrar anger vilka åtgärder för innehållsmoderering som kommer att göras. I det här exemplet konfigureras metoden för att:
- Identifiera potentiellt olämpligt språk i texten.
- Normalisera texten och autokorrigera stavfel.
- Identifiera personligt identifierbar information (PII), till exempel telefonnummer i USA och Storbritannien, e-postadresser och amerikanska postadresser.
- Använd maskininlärningsbaserade modeller för att klassificera texten i tre kategorier.

Om du vill veta mer om vad de här åtgärderna gör följer du länken i avsnittet [Nästa steg](#next-steps).

```csharp
// Load the input text.
string text = File.ReadAllText(TextFile);
Console.WriteLine("Screening {0}", TextFile);

text = text.Replace(System.Environment.NewLine, " ");
byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(text);
MemoryStream stream = new MemoryStream(byteArray);

// Save the moderation results to a file.
using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
{
    // Create a Content Moderator client and evaluate the text.
    using (var client = Clients.NewClient())
    {
        // Screen the input text: check for profanity,
        // autocorrect text, check for personally identifying
        // information (PII), and classify the text into three categories
        outputWriter.WriteLine("Autocorrect typos, check for matching terms, PII, and classify.");
        var screenResult =
        client.TextModeration.ScreenText("text/plain", stream, "eng", true, true, null, true);
        outputWriter.WriteLine(
                JsonConvert.SerializeObject(screenResult, Formatting.Indented));
    }
    outputWriter.Flush();
    outputWriter.Close();
}
```

## <a name="run-the-program"></a>Köra programmet

Programmet skriver JSON-strängdata till filen _TextModerationOutput.txt_. Exempeltexten som används i den här snabbstarten ger följande utdata:

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>Nästa steg

Du har utvecklat ett enkelt .NET-program som använder Content Moderator-tjänsten för att hitta relevant information om ett angivet textexempel i den här snabbstarten. Därefter kan du läsa mer om vad de olika flaggorna och klassificeringarna betyder så att du kan avgöra vilka data du behöver och hur din app ska hantera dem.

> [!div class="nextstepaction"]
> [Guide för textmoderering](text-moderation-api.md)
