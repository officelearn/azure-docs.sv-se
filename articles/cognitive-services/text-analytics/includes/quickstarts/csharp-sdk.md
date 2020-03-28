---
title: 'Snabbstart: Text Analytics klientbibliotek för C# | Microsoft-dokument'
description: 'Komma igång med Text Analytics-klientbiblioteket för C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 64eb19e43223c1953a7244f8fd29c48d085f1e96
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80116858"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

[v3 Referensdokumentation](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 Biblioteks källkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3-prover](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[v2 Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 Biblioteks källkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2-prover](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* När du har din <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-prenumeration"  target="_blank">skapar Skapa en <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics-resurs en Text Analytics-resurs i Azure-portalen för att hämta din nyckel och slutpunkt.  När den har distribuerats klickar du på **Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från den resurs du skapar för att ansluta ditt program till Text Analytics API. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den`F0`kostnadsfria prisnivån ( ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

Skapa en ny .NET Core-konsolapp med Visual Studio IDE. Detta kommer att skapa ett "Hello World"-projekt med en enda C#-källfil: *program.cs*.

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Installera klientbiblioteket genom att högerklicka på lösningen i **Lösningsutforskaren** och välja **Hantera NuGet-paket**. I pakethanteraren som öppnar välj **Bläddra,** kontrollera **Inkludera förhandsversion**och sök efter `Azure.AI.TextAnalytics`. Välj `1.0.0-preview.3`version och **installera**sedan . Du kan också använda [Package Manager Console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), som innehåller kodexemplen i den här snabbstarten. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Installera klientbiblioteket genom att högerklicka på lösningen i **Lösningsutforskaren** och välja **Hantera NuGet-paket**. I pakethanteraren som öppnas **Browse** väljer du `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`Bläddra och sök efter . Klicka på den och **installera**sedan . Du kan också använda [Package Manager Console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), som innehåller kodexemplen i den här snabbstarten. 

---

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Öppna *program.cs* filen och lägg `using` till följande direktiv:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Skapa variabler `Program` för resursens nyckel och slutpunkt i programmets klass.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Ersätt programmets `Main` metod. Du kommer att definiera de metoder som kallas här senare.

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

Öppna *program.cs* filen och lägg `using` till följande direktiv:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Skapa variabler `Program` för resursens nyckel och slutpunkt i programmets klass. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Ersätt programmets `Main` metod. Du kommer att definiera de metoder som kallas här senare.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektmodell

Text Analytics-klienten `TextAnalyticsClient` är ett objekt som autentiserar till Azure med hjälp av nyckeln och tillhandahåller funktioner för att acceptera text som enstaka strängar eller som en batch. Du kan skicka text till API-programmet synkront eller asynkront. Svarsobjektet innehåller analysinformationen för varje dokument som du skickar. 

Om du använder `3.0-preview`version kan du `TextAnalyticsClientOptions` använda en valfri instans för att initiera klienten med olika standardinställningar (till exempel standardspråk eller landstips). Du kan också autentisera med en Azure Active Directory-token. 

## <a name="code-examples"></a>Kodexempel

* [Sentimentanalys](#sentiment-analysis)
* [Språkidentifiering](#language-detection)
* [Namngiven entitetsigenkänning](#named-entity-recognition-ner)
* [Identifiera personlig information](#detect-personal-information)
* [Entitetslänkning](#entity-linking)
* [Extraktion av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Kontrollera att din huvudmetod från tidigare skapar ett nytt klientobjekt med slutpunkten och autentiseringsuppgifterna.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en `ApiKeyServiceClientCredentials` ny klass för att lagra autentiseringsuppgifterna och lägga till dem i klientens begäranden. I den skapar du `ProcessHttpRequestAsync()` en åsidosättning `Ocp-Apim-Subscription-Key` för den som lägger till nyckeln i sidhuvudet.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Skapa en metod för att instansiera [TextAnalyticsClient-objektet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) med slutpunkten och ett `ApiKeyServiceClientCredentials` objekt som innehåller nyckeln.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalys

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en ny `SentimentAnalysisExample()` funktion som anropas som tar `AnalyzeSentiment()` klienten som du skapade tidigare och anropa dess funktion. Det returnerade `Response<DocumentSentiment>` objektet innehåller sentimentetiketten och poängen för hela indatadokumentet, samt en sentimentanalys för varje mening om det lyckas. Om det fanns ett fel, `RequestFailedException`kommer det att kasta en .

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

Skapa en ny `SentimentAnalysisExample()` funktion som anropas som tar klienten som du skapade tidigare och anropar funktionen [Sentiment().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) Det returnerade [SentimentResult-objektet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) `Score` innehåller sentimentet `errorMessage` om det lyckas och ett om inte. 

En poäng som är nära 0 indikerar en negativ känsla, medan en poäng som är närmare 1 indikerar en positiv känsla.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Språkidentifiering

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)


Skapa en ny `LanguageDetectionExample()` funktion som anropas som tar `DetectLanguage()` klienten som du skapade tidigare och anropa dess funktion. Det returnerade `Response<DetectedLanguage>` objektet kommer att innehålla det identifierade språket tillsammans med dess namn och ISO-6391-kod. Om det fanns ett fel, `RequestFailedException`kommer det att kasta en .

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indata. Du kan `countryHint` använda parametern för att ange en landskod med två bokstäver. Som standard api använder "USA" som standardlandInta, för att ta bort detta beteende kan `countryHint = ""`du återställa den här parametern genom att ställa in det här värdet till tom sträng . Om du vill ange `TextAnalyticsClientOptions.DefaultCountryHint` en annan standard anger du egenskapen och skickar den under klientens initiering.

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

Skapa en ny `languageDetectionExample()` funktion som anropas som tar klienten som du skapade tidigare och anropar funktionen [DetectLanguage().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Det returnerade [LanguageResult-objektet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) innehåller listan `DetectedLanguages` över identifierade språk `errorMessage` i om det lyckas och en om inte. Skriv ut det första returnerade språket.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indata. Du kan `countryHint` använda parametern för att ange en landskod med två bokstäver. Som standard api använder "USA" som standardlandInta, för att ta bort detta beteende kan `countryHint = ""` du återställa den här parametern genom att ställa in det här värdet till tom sträng .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Resultat

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Namngiven entitetsigenkänning (NER)

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)


> [!NOTE]
> Ny i `3.0-preview`version:
> * Entitetsigenkänning innehåller nu möjligheten att identifiera personlig information i text.
> * Entitetslänkning är nu en separerad från entitetsredovisning.


Skapa en ny `EntityRecognitionExample()` funktion som kallas som tar `RecognizeEntities()` klienten som du skapade tidigare, anropa dess funktion och iterera genom resultaten. Det returnerade `Response<IReadOnlyCollection<CategorizedEntity>>` objektet innehåller en lista över identifierade entiteter. Om det fanns ett fel, `RequestFailedException`kommer det att kasta en .

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

## <a name="detect-personal-information"></a>Identifiera personlig information

Skapa en ny `EntityPIIExample()` funktion som kallas som tar `RecognizePiiEntities()` klienten som du skapade tidigare, anropa dess funktion och iterera genom resultaten. I likhet med föregående `Response<IReadOnlyCollection<CategorizedEntity>>` funktion kommer det returnerade objektet att innehålla en lista över identifierade entiteter. Om det fanns ett fel, `RequestFailedException`kommer det att kasta en .

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

Skapa en ny `EntityLinkingExample()` funktion som kallas som tar `RecognizeLinkedEntities()` klienten som du skapade tidigare, anropa dess funktion och iterera genom resultaten. Den returnerade `Response<IReadOnlyCollection<LinkedEntity>>` representerar listan över identifierade entiteter. Om det fanns ett fel, `RequestFailedException`kommer det att kasta en . Eftersom länkade entiteter identifieras unikt grupperas förekomster `LinkedEntity` av samma `LinkedEntityMatch` entitet under ett objekt som en lista över objekt.

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
> I version 2.1 inkluderas entitetslänkning i NER-svaret.

Skapa en ny `RecognizeEntitiesExample()` funktion som anropas som tar klienten som du skapade tidigare och anropar funktionen [Entiteter().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Iterera genom resultaten. Det returnerade [Entitetsresult-objektet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) innehåller listan `Entities` över identifierade `errorMessage` entiteter i om det lyckas och en om inte. För varje identifierad entitet skriver du ut dess typ, undertyp, Wikipedia-namn (om sådana finns) samt platserna i den ursprungliga texten.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en ny `KeyPhraseExtractionExample()` funktion som anropas som tar `ExtractKeyPhrases()` klienten som du skapade tidigare och anropa dess funktion. Det returnerade `<Response<IReadOnlyCollection<string>>` objektet innehåller en lista över identifierade nyckelfraser. Om det fanns ett fel, `RequestFailedException`kommer det att kasta en .

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

Skapa en ny `KeyPhraseExtractionExample()` funktion som anropas som tar klienten som du skapade tidigare och anropar dess [nyckelfras()-funktion.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Resultatet kommer att innehålla en lista `KeyPhrases` över identifierade nyckelfraser i om det lyckas, och en `errorMessage` om inte. Skriv ut alla identifierade nyckelfraser.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Resultat

```console
Key phrases:
    cat
    veterinarian
```

---
