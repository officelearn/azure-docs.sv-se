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
ms.date: 07/19/2019
ms.author: assafi
ms.openlocfilehash: 5e5246ab46d00e0b62b3dee321c6cea893e6a5d3
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619625"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Snabbstart: Använd .NET SDK och C# för att anropa tjänsten textanalys
<a name="HOLTop"></a>

Den här snabb starten hjälper dig att börja använda Azure SDK för C# .net och att analysera språk. Även om [Textanalys](//go.microsoft.com/fwlink/?LinkID=759711) REST API är kompatibel med de flesta programmeringsspråk, är SDK ett enkelt sätt att integrera tjänsten i dina program.

> [!NOTE]
> Demo koden i den här artikeln använder de synkrona metoderna i Textanalys .NET SDK för enkelhetens skull. För produktions scenarier rekommenderar vi dock att du använder batch-asynkrona metoder i dina egna program för att hålla dem skalbara och tillgängliga. Du kan till exempel använda `SentimentBatchAsync` i stället för. `Sentiment`
>
> Du hittar en batch-asynkron version av det här exemplet på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Teknisk information finns i SDK för .NET [textanalys referens](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Förutsättningar

* Valfri version av Visual Studio 2017 eller senare
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
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

I följande avsnitt beskrivs hur du anropar varje tjänst funktion.

## <a name="perform-sentiment-analysis"></a>Utför sentiment-analys

1. Skapa en ny funktion `SentimentAnalysisExample()` som tar klienten som du skapade tidigare.
2. Anropa `client.Sentiment()` och hämta resultatet i samma funktion. Resultatet kommer att innehålla sentiment `Score` om det lyckas, och en `errorMessage` om inte. En poäng som är nära 0 anger ett negativt sentiment, medan poängen är närmare 1 betyder en positiv sentiment.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Utför språk identifiering

1. Skapa en ny funktion `DetectLanguageExample()` som tar klienten som du skapade tidigare.
2. Anropa `client.DetectLanguage()` och hämta resultatet i samma funktion. Resultatet kommer att innehålla en lista över identifierade språk `DetectedLanguages` i om det lyckas, `errorMessage` och en om inte. Skriv sedan ut det första returnerade språket.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda `countryHint` -parametern för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange värdet `countryHint = ""` till en tom sträng.

### <a name="output"></a>Output

```console
Language: English
```

## <a name="perform-entity-recognition"></a>Utför enhets igenkänning

1. Skapa en ny funktion `RecognizeEntitiesExample()` som tar klienten som du skapade tidigare.
2. Anropa `client.Entities()` och hämta resultatet i samma funktion. Iterera sedan igenom resultaten. Resultatet kommer att innehålla listan över identifierade entiteter `Entities` i om det lyckas, `errorMessage` och en om inte. För varje identifierad entitet skriver du ut dess typ, undertyp, Wikipedia-namn (om de finns) samt platserna i den ursprungliga texten.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>Output

```console
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
2. Anropa `client.KeyPhrases()` och hämta resultatet i samma funktion. Resultatet kommer att innehålla en lista över identifierade nyckel fraser `KeyPhrases` i om det lyckas, `errorMessage` och en om inte. Skriv sedan ut identifierade nyckel fraser.

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Översikt över Textanalys](../overview.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
