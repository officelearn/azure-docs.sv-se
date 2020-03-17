---
title: 'Snabbstart: Textanalys-klientbiblioteket för C# | Microsoft Docs'
description: Kom igång med Textanalys-klientbiblioteket för C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 6adce0ed6b5b5768bd9a489fced25ce439a33e0a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203442"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

[Referensdokumentation för v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Bibliotekskällkod för v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Paket för v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3-exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[Referensdokumentation för v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Bibliotekskällkod för v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Paket för v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [Skapa en kostnadsfritt](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Skapa en resurs för Textanalys"  target="_blank">skapa en resurs för Textanalys <span class="docon docon-navigate-external x-hidden-focus"></span></a> i Microsoft Azure-portalen för att hämta din nyckel och slutpunkt. 
    * Du behöver nyckeln och slutpunkten från resursen som du skapade för att ansluta ditt program till API för textanalys. Du kommer att göra detta senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån för att testa tjänsten och uppgradera till en betald nivå för produktion senare.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-net-core-application"></a>Skapa en ny .NET Core-app

Skapa en ny .NET Core-konsolapp med Visual Studio IDE. Det här skapar ett ”Hello World”-projekt med en enda C#-källfil: *program.cs*.

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Installera klientbiblioteket genom att högerklicka på lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I pakethanteraren som öppnas väljer du **Browse** (Bläddra), markera **Include prerelease** (Inkludera förhandsversion) och söker efter `Azure.AI.TextAnalytics`. Välj version `1.0.0-preview.2` och sedan **Installera**. Du kan också använda [Package Manager-konsolen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), som innehåller kodexemplen i den här snabbstarten. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Installera klientbiblioteket genom att högerklicka på lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I pakethanteraren som öppnas väljer du **Browse** (Bläddra) och söker efter `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klicka på det och sedan på **Install** (Installera). Du kan också använda [Package Manager-konsolen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), som innehåller kodexemplen i den här snabbstarten. 

---

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Öppna filen *program.cs* och lägg till följande `using`-instruktioner:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

I programmets `Program`-klass skapar du variabler för resursens nyckel och slutpunkt.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Ersätt programmets `Main`-metod. Du definierar de anropade metoderna här senare.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Öppna filen *program.cs* och lägg till följande `using`-instruktioner:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

I programmets `Program`-klass skapar du variabler för resursens nyckel och slutpunkt. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Ersätt programmets `Main`-metod. Du definierar de anropade metoderna här senare.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektmodell

Textanalys-klienten är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med hjälp av din nyckel och ger funktioner för att acceptera text som enkla strängar eller som en batch. Du kan skicka text till API:et synkront eller asynkront. Svarsobjektet kommer att innehålla analysinformationen om varje dokument du skickar. 

Om du använder version `3.0-preview` kan du använda en valfri `TextAnalyticsClientOptions`-instans för att initiera klienten med olika standardinställningar (till exempel standardspråk eller landstips). Du kan också autentisera med hjälp av en Azure Active Directory-token. 

## <a name="code-examples"></a>Kodexempel

* [Attitydanalys](#sentiment-analysis)
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven enhet](#named-entity-recognition-ner)
* [Identifiering av personlig information](#detect-personal-information)
* [Entitetslänkning](#entity-linking)
* [Extrahering av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Kontrollera att din main-metod från tidigare skapar ett nytt klientobjekt med din slutpunkt och dina autentiseringsuppgifter.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en ny `ApiKeyServiceClientCredentials`-klass för att lagra autentiseringsuppgifterna och lägga till dem i klientens begäranden. Skapa en åsidosättning för `ProcessHttpRequestAsync()` i den som lägger till nyckeln i `Ocp-Apim-Subscription-Key`-huvudet.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Skapa en metod för att instansiera [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet)-objektet med din slutpunkt och ett `ApiKeyServiceClientCredentials`-objekt som innehåller din nyckel.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalys

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en ny funktion med namnet `SentimentAnalysisExample()` som tar klienten du har skapat tidigare och anropar dess `AnalyzeSentiment()`-funktion. Det returnerade `Response<DocumentSentiment>`-objektet innehåller sentiment-etiketten och resultatet för hela indatadokumentet, samt en attitydanalys för varje mening om det lyckas. Om det uppstår ett fel utlöses ett `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [length {sentence.GraphemeLength}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.GraphemeOffset, sentence.GraphemeLength)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Resultat

```console
Document sentiment: Positive

        Sentence [length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en ny funktion med namnet `SentimentAnalysisExample()` som tar klienten du har skapat tidigare och anropar dess [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet)-funktion. Det returnerade [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet)-objektet innehåller sentimentet `Score` om det lyckas och ett `errorMessage` om det misslyckas. 

Ett resultat som är nära 0 visar negativt sentiment medan ett resultat som är nära 1 indikerar ett positivt sentiment.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Språkidentifiering

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)


Skapa en ny funktion med namnet `LanguageDetectionExample()` som tar klienten du har skapat tidigare och anropar dess `DetectLanguage()`-funktion. Det returnerade `Response<DetectedLanguage>`-objektet innehåller det identifierade språket tillsammans med dess namn och ISO-6391-kod. Om det uppstår ett fel utlöses ett `RequestFailedException`.

> [!Tip]
> I vissa fall kan det vara svårt att förtydliga språk baserat på indata. Du kan använda parametern `countryHint` för att ange en landskod på 2 bokstäver. Som standard använder API:et ”US” som standardinställd countryHint. Om du vill ta bort detta beteende kan du återställa parametern genom att ställa in värdet på tom sträng, `countryHint = ""`. Om du ställa in ett annat standardvärde anger du egenskapen `TextAnalyticsClientOptions.DefaultCountryHint` och skickar den under klientens initiering.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Resultat

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en ny funktion med namnet `languageDetectionExample()` som tar klienten du har skapat tidigare och anropar dess [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_)-funktion. Det returnerade [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet)-objektet innehåller listan över identifierade språk i `DetectedLanguages` om det lyckas och ett `errorMessage` om det misslyckas. Skriv ut det första returnerade språket.

> [!Tip]
> I vissa fall kan det vara svårt att förtydliga språk baserat på indata. Du kan använda parametern `countryHint` för att ange en landskod på 2 bokstäver. Som standard använder API:et ”US” som standardinställd countryHint. Om du vill ta bort detta beteende kan du återställa parametern genom att ställa in värdet på tom sträng, `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Resultat

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngiven enhet (NER)

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)


> [!NOTE]
> Nytt i version `3.0-preview`:
> * I entitetsidentifiering ingår nu möjligheten att identifiera personlig information i text.
> * Entitetslänkning är nu skild från entitetsidentifiering.


Skapa en ny funktion med namnet `EntityRecognitionExample()` som tar klienten du har skapat tidigare, anropar dess `RecognizeEntities()`-funktion och itererar genom resultatet. Det returnerade `Response<IReadOnlyCollection<CategorizedEntity>>`-objektet kommer att innehålla listan över identifierade entiteter. Om det uppstår ett fel utlöses ett `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Resultat

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Length: 7,      Score: 0.92

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Length: 9,      Score: 0.80
```

## <a name="detect-personal-information"></a>Identifiering av personlig information

Skapa en ny funktion med namnet `EntityPIIExample()` som tar klienten du har skapat tidigare, anropar dess `RecognizePiiEntities()`-funktion och itererar genom resultatet. Liksom för den föregående funktionen kommer det returnerade `Response<IReadOnlyCollection<CategorizedEntity>>`-objektet att innehålla listan över identifierade entiteter. Om det uppstår ett fel utlöses ett `RequestFailedException`.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Resultat

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category:
                Length: 11,     Score: 0.85
```


## <a name="entity-linking"></a>Entitetslänkning

Skapa en ny funktion med namnet `EntityLinkingExample()` som tar klienten du har skapat tidigare, anropar dess `RecognizeLinkedEntities()`-funktion och itererar genom resultatet. Det returnerade `Response<IReadOnlyCollection<LinkedEntity>>` representerar listan över identifierade entiteter. Om det uppstår ett fel utlöses ett `RequestFailedException`. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `LinkedEntity`-objekt som en lista över `LinkedEntityMatch`-objekt.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tLength: {match.GraphemeLength},\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Resultat

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Length: 11,     Score: 0.78

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Length: 10,     Score: 0.55

                Text: Gates
                Length: 5,      Score: 0.55

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Length: 10,     Score: 0.53

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Length: 9,      Score: 0.47

                Text: Microsoft
                Length: 9,      Score: 0.47

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Length: 7,      Score: 0.25

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Length: 5,      Score: 0.28
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

> [!NOTE]
> I version 2.1 ingår entitetslänkning i NER-svaret.

Skapa en ny funktion med namnet `RecognizeEntitiesExample()` som tar klienten du har skapat tidigare och anropar dess [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_)-funktion. Iterera genom resultatet. Det returnerade [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet)-objektet innehåller listan över identifierade entiteter `Entities` om det lyckas och ett `errorMessage` om det misslyckas. Skriv ut typ, undertyp Wikipedia-namn (om de finns) samt platserna i den ursprungliga texten för varje identifierad entitet.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en ny funktion med namnet `KeyPhraseExtractionExample()` som tar klienten du har skapat tidigare och anropar dess `ExtractKeyPhrases()`-funktion. Det returnerade `<Response<IReadOnlyCollection<string>>`-objektet kommer att innehålla listan över identifierade nyckelfraser. Om det uppstår ett fel utlöses ett `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Resultat

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en ny funktion med namnet `KeyPhraseExtractionExample()` som tar klienten du har skapat tidigare och anropar dess [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_)-funktion. Resultatet innehåller listan över identifierade entiteter `KeyPhrases` om det lyckas och ett `errorMessage` om det misslyckas. Skriv ut identifierade nyckelfraser.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Resultat

```console
Key phrases:
    cat
    veterinarian
```

---
