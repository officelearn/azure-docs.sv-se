---
title: 'Snabbstart: Anropa API:et för textanalys med hjälp av C#'
titleSuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API för textanalys.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/29/2019
ms.author: assafi
ms.openlocfilehash: e7b07472623cc459c31906aeaa6ccfb4388b4b50
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146090"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Snabbstart: Anropa den kognitiva tjänsten för textanalys med hjälp av C#
<a name="HOLTop"></a>

Använd den här snabbstarten för att börja analysera språk med Text Analytics SDK för C#. Medan den [textanalys](//go.microsoft.com/fwlink/?LinkID=759711) REST API är kompatibelt med de flesta programmeringsspråk, SDK innehåller ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Se [API-definitionerna](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) för teknisk dokumentation för API:erna.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Du måste även ha [slutpunkten och åtkomstnyckeln](../How-tos/text-analytics-how-to-access-key.md) som genererades åt dig vid registreringen.

> [!Tip]
>  Du skulle kunna anropa [HTTP-slutpunkterna](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) direkt från C#, men Microsoft.Azure.CognitiveServices.Language SDK gör det mycket enklare att anropa tjänsten utan att behöva bry sig om serialisering och avserialisering av JSON.
>
> Några användbara länkar:
> - [SDK Nuget-sidan](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK-kod](https://github.com/Azure/azure-sdk-for-net/tree/master/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Skapa Visual Studio-lösningen och installera SDK

1. Skapa ett nytt konsolprogram (.NET Core)-projekt [Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Högerklicka på lösningen och klicka på **Hantera NuGet-paket för lösningen**
1. Välj fliken **Bläddra** sök efter **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**

## <a name="authenticate-your-credentials"></a>Autentisera dina autentiseringsuppgifter

1. Lägg till följande `using` instruktioner i filen för main-klass (`Program.cs` som standard).

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

2. Skapa en ny `ApiKeyServiceClientCredentials` klassen för att spara autentiseringsuppgifterna och lägga till dem för varje begäran.

    ```csharp
    /// <summary>
    /// Allows authentication to the API using a basic apiKey mechanism
    /// </summary>
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string subscriptionKey;

        /// <summary>
        /// Creates a new instance of the ApiKeyServiceClientCredentails class
        /// </summary>
        /// <param name="subscriptionKey">The subscription key to authenticate and authorize as</param>
        public ApiKeyServiceClientCredentials(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
        }

        /// <summary>
        /// Add the Basic Authentication Header to each outgoing request
        /// </summary>
        /// <param name="request">The outgoing request</param>
        /// <param name="cancellationToken">A token to cancel the operation</param>
        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }

            request.Headers.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Uppdatera den `Program` klassen och Lägg till medlem konstant för din prenumerationsnyckel för textanalys och en annan för tjänsteslutpunkt. Kom ihåg att använda rätt Azure-regionen för din Text Analytics-prenumeration.

    ```csharp
    private const string SubscriptionKey = "enter-your-key-here";

    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://westus.api.cognitive.microsoft.com";
    ```
> [!Tip]
> För säker distribution av hemligheter i produktionssystem bör du använda [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)
>

## <a name="create-a-text-analytics-client"></a>Skapa en Text Analytics-klient

I den `Main` funktion i ditt projekt, anropa metoden exemplet som du vill anropa och skicka den `Endpoint` och `SubscriptionKey` parametrar som angetts.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(SubscriptionKey);
        var client = new TextAnalyticsClient(credentials)
        {
            //Replace 'westus' with the correct region for your Text Analytics subscription
            Endpoint = "https://westus.api.cognitive.microsoft.com"
        };

        // Change console encoding to display non-ASCII characters
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

Nästa avsnitt beskriver hur du anropar var och en av API-funktioner.

## <a name="sentiment-analysis"></a>Sentimentanalys

1. Skapa en ny funktion som kallas `SentimentAnalysisExample()` som tar klienten som skapade tidigare.
2. Skapa en ny `TextAnalyticsClient` objekt med `ApiKeyServiceClientCredentials` som en parameter och generera en lista över `MultiLanguageInput` objekt, som innehåller de dokument som du vill analysera.

    ```csharp
    public static async Task SentimentAnalysisExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life."),
                new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
            });
        //...
    }
    ```

3. I samma funktion, anropa `client.SentimentAsync()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID och attitydsresultatet. Ett värde närmare 0 anger negativ känsla, medan en poäng närmare 1 anger positiv känsla.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Resultat

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Språkspårning

1. Skapa en ny funktion som kallas `DetectLanguageExample()` som tar klienten som skapade tidigare.
2. Skapa en ny `TextAnalyticsClient` objekt med `ApiKeyServiceClientCredentials` som en parameter och generera en lista över `LanguageInput` objekt, som innehåller dina dokument.

    ```csharp
    public static async Task DetectLanguageExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English."),
                        new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                        new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                    });
        //...
    }
    ```

3. I samma funktion, anropa `client.DetectLanguageAsync()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID och det första språket som returneras.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Resultat

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Igenkänning av entiteter

1. Skapa en ny funktion som kallas `RecognizeEntitiesExample()` som tar klienten som skapade tidigare.
2. Skapa en ny `TextAnalyticsClient` objekt med `ApiKeyServiceClientCredentials` som en parameter och generera en lista över `MultiLanguageBatchInput` objekt, som innehåller dina dokument.

    ```csharp
    public static async Task RecognizeEntitiesExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
            });
        //...
    }
    ```

3. I samma funktion, anropa `client.EntitiesAsync()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID. För varje identifierad entitet, skriva ut dess wikipedia namn, typ och undertyper (om det finns) samt platser i den ursprungliga texten.

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

### <a name="output"></a>Resultat

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
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

1. Skapa en ny funktion som kallas `KeyPhraseExtractionExample()` som tar klienten som skapade tidigare.
2. Skapa en ny `TextAnalyticsClient` objekt med `ApiKeyServiceClientCredentials` som en parameter och generera en lista över `MultiLanguageBatchInput` objekt, som innehåller dina dokument.

    ```csharp
    public static async Task KeyPhraseExtractionExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat might need to see a veterinarian."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    });
        //...
    }
    ```

3. I samma funktion, anropa `client.KeyPhrasesAsync()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID och alla identifierade viktiga fraser.

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

### <a name="output"></a>Resultat

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Översikt över Textanalys](../overview.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)

