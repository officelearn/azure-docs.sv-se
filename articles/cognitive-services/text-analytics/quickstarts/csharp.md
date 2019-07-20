---
title: 'Snabbstart: Anropa tjänsten Textanalys med hjälp av Azure SDK för .NET ochC#'
titleSuffix: Azure Cognitive Services
description: Information och kod exempel som hjälper dig att börja använda Textanalys-tjänsten C#och.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356973"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Snabbstart: Använd .NET SDK och C# för att anropa tjänsten textanalys
<a name="HOLTop"></a>

Den här snabb starten hjälper dig att börja använda Azure SDK för C# .net och att analysera språk. Även om [Textanalys](//go.microsoft.com/fwlink/?LinkID=759711) REST API är kompatibel med de flesta programmeringsspråk, är SDK ett enkelt sätt att integrera tjänsten i dina program.

> [!NOTE]
> Källkoden till det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Teknisk information finns i SDK för .NET [textanalys referens](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Förutsättningar

* Valfri utgåva av [Visual Studio 2017 eller senare]
* Textanalys [SDK för .net](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Du behöver också [slut punkten och åtkomst nyckeln](../How-tos/text-analytics-how-to-access-key.md) som genererades åt dig under registreringen.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Skapa Visual Studio-lösningen och installera SDK: n

1. Skapa ett nytt projekt för konsol program (.NET Core). [Få åtkomst till Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Högerklicka på lösningen och välj **Hantera NuGet-paket för lösningen**.
1. Välj fliken **Bläddra**. Sök efter **Microsoft. Azure. CognitiveServices. language. TextAnalytics**.

## <a name="authenticate-your-credentials"></a>Autentisera dina autentiseringsuppgifter

1. Lägg till följande `using` -instruktioner i huvud klass filen (som är program.cs som standard).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Skapa en ny `ApiKeyServiceClientCredentials` klass för att lagra autentiseringsuppgifterna och lägga till dem för varje begäran.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Uppdatera- `Program` klassen. Lägg till en konstant medlem för din API för textanalys nyckel och en annan för tjänst slut punkten. Kom ihåg att använda rätt Azure-plats för din Textanalys-resurs.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```
> [!Tip]
> För att öka säkerheten för hemligheter i produktions system rekommenderar vi att du använder [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Skapa en Textanalys-klient

Anropa den exempel metod som du vill anropa i projektets funktion.`Main` Överför de `Endpoint` och `ApiKey` parametrarna som du har definierat.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

I följande avsnitt beskrivs hur du anropar varje tjänst funktion.

## <a name="perform-sentiment-analysis"></a>Utför sentiment-analys

1. Skapa en ny funktion `SentimentAnalysisExample()` som tar klienten som du skapade tidigare.
2. Generera en lista med `MultiLanguageInput` objekt som innehåller dokumenten som du vill analysera.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. Anropa `client.SentimentAsync()` och hämta resultatet i samma funktion. Iterera sedan igenom resultaten. Skriv ut varje dokuments ID och sentiment poäng. En poäng som är nära 0 anger ett negativt sentiment, medan poängen är närmare 1 betyder en positiv sentiment.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Utför språk identifiering

1. Skapa en ny funktion `DetectLanguageExample()` som tar klienten som du skapade tidigare.
2. Generera en lista med `LanguageInput` objekt som innehåller dina dokument.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. Anropa `client.DetectLanguageAsync()` och hämta resultatet i samma funktion. Iterera sedan igenom resultaten. Skriv ut varje dokuments ID och det första returnerade språket.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
```

## <a name="perform-entity-recognition"></a>Utför enhets igenkänning

1. Skapa en ny funktion `RecognizeEntitiesExample()` som tar klienten som du skapade tidigare.
2. Generera en lista med `MultiLanguageBatchInput` objekt som innehåller dina dokument.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. Anropa `client.EntitiesAsync()` och hämta resultatet i samma funktion. Iterera sedan igenom resultaten. Skriv ut varje dokuments ID. För varje identifierad entitet skriver du ut dess Wikipedia-namn och typ och under typer (om de finns) samt platserna i den ursprungliga texten.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>Utför extrahering av nyckel fraser

1. Skapa en ny funktion `KeyPhraseExtractionExample()` som tar klienten som du skapade tidigare.
2. Generera en lista med `MultiLanguageBatchInput` objekt som innehåller dina dokument.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. Anropa `client.KeyPhrasesAsync()` och hämta resultatet i samma funktion. Iterera sedan igenom resultaten. Skriv ut varje dokuments ID och identifierade nyckel fraser.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                cat
                veterinarian
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Översikt över Textanalys](../overview.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
