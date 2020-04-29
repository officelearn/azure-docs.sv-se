---
title: 'Snabb start: Textanalys klient bibliotek för C# | Microsoft Docs'
description: 'Kom igång med Textanalys klient biblioteket för C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2fa2e40ba2a7fe84b6df57bfb711d01332b8f523
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275423"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

[v3 referens dokumentation](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 bibliotek käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3-paket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3-exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

[v2 referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 paket för biblioteks käll kods](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2-paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* När du har en Azure <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="-prenumeration skapar du en textanalys"  target="_blank">resurs skapa en textanalys <span class="docon docon-navigate-external x-hidden-focus"></span> </a> resurs i Azure Portal för att hämta din nyckel och slut punkt.  När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API för textanalys. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån`F0`() för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

Skapa en ny .NET Core-konsol med hjälp av Visual Studio IDE. Då skapas ett "Hello World"-projekt med en enda C#-källfil: *program.cs*.

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

Installera klient biblioteket genom att högerklicka på lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I paket hanteraren som öppnas väljer du **Bläddra**, markerar **ta med för hands version**och söker `Azure.AI.TextAnalytics`efter. Välj version `1.0.0-preview.4`och **Installera**sedan. Du kan också använda [Package Manager-konsolen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), som innehåller kod exemplen i den här snabb starten. 

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

Installera klient biblioteket genom att högerklicka på lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I den paket hanterare som öppnas väljer du **Bläddra** och Sök efter `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klicka på den och **Installera**sedan. Du kan också använda [Package Manager-konsolen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den [på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), som innehåller kod exemplen i den här snabb starten. 

---

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

Öppna filen *program.cs* och Lägg till följande `using` direktiv:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

I programmets `Program` klass skapar du variabler för resursens nyckel och slut punkt.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Ersätt programmets `Main` metod. Du definierar de metoder som anropas här senare.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

Öppna filen *program.cs* och Lägg till följande `using` direktiv:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

I programmets `Program` klass skapar du variabler för resursens nyckel och slut punkt. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Ersätt programmets `Main` metod. Du definierar de metoder som anropas här senare.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett `TextAnalyticsClient` objekt som autentiserar till Azure med hjälp av din nyckel och ger funktioner för att acceptera text som enkla strängar eller som en batch. Du kan skicka text till API: et synkront eller asynkront. Objektet Response kommer att innehålla analys informationen för varje dokument som du skickar. 

Om du använder en version `3.0-preview` av tjänsten kan du använda en valfri `TextAnalyticsClientOptions` instans för att initiera klienten med olika standardinställningar (till exempel standard språk eller lands tips). Du kan också autentisera med hjälp av en Azure Active Directory-token. 

## <a name="code-examples"></a>Kodexempel

* [Sentimentanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Igenkänning av namngiven enhet](#named-entity-recognition-ner)
* [Länkning av entitet](#entity-linking)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

Kontrol lera att din huvud metod från tidigare skapar ett nytt klient objekt med din slut punkt och dina autentiseringsuppgifter.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

Skapa en ny `ApiKeyServiceClientCredentials` klass för att lagra autentiseringsuppgifterna och lägga till dem i klientens begär Anden. I den skapar du en åsidosättning för `ProcessHttpRequestAsync()` som lägger till din nyckel i `Ocp-Apim-Subscription-Key` rubriken.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Skapa en metod för att instansiera [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -objektet med din slut punkt `ApiKeyServiceClientCredentials` och ett objekt som innehåller din nyckel.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalys

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

Skapa en ny funktion som `SentimentAnalysisExample()` anropar den klient som du skapade tidigare och anropa dess `AnalyzeSentiment()` funktion. Det returnerade `Response<DocumentSentiment>` objektet kommer att innehålla sentiment etikett och poängen i hela indatamängden, samt en sentiment-analys för varje mening om det lyckas. Om ett fel uppstår kommer det att utlösa en `RequestFailedException`.

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

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

Skapa en ny funktion som `SentimentAnalysisExample()` anropar den klient som du skapade tidigare och anropa dess [sentiment ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) funktion. Det returnerade [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) -objektet kommer att `Score` innehålla sentiment om det lyckas `errorMessage` , och en om inte. 

En poäng som är nära 0 anger ett negativt sentiment, medan poängen är närmare 1 betyder en positiv sentiment.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Språkidentifiering

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)


Skapa en ny funktion som `LanguageDetectionExample()` anropar den klient som du skapade tidigare och anropa dess `DetectLanguage()` funktion. Det returnerade `Response<DetectedLanguage>` objektet kommer att innehålla det identifierade språket tillsammans med dess namn och ISO-6391-koden. Om ett fel uppstår kommer det att utlösa en `RequestFailedException`.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda- `countryHint` parametern för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange värdet `countryHint = ""`till en tom sträng. Om du vill ange en annan standard ställer `TextAnalyticsClientOptions.DefaultCountryHint` du in egenskapen och skickar den under klient initieringen.

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

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

Skapa en ny funktion som `languageDetectionExample()` anropar den klient som du skapade tidigare och anropa dess [DetectLanguage ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) funktion. Det returnerade [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) -objektet kommer att innehålla en lista över `DetectedLanguages` identifierade språk i om `errorMessage` det lyckas, och en om inte. Skriv ut det första returnerade språket.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda- `countryHint` parametern för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange värdet `countryHint = ""` till en tom sträng.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Resultat

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)


> [!NOTE]
> Ny i version `3.0-preview`:
> * Enhets länkning är nu avskilt från entitets igenkänning.


Skapa en ny funktion som `EntityRecognitionExample()` anropar den klient som du skapade tidigare, anropa sin `RecognizeEntities()` funktion och iterera genom resultaten. Det returnerade `Response<IReadOnlyCollection<CategorizedEntity>>` objektet kommer att innehålla en lista över identifierade entiteter. Om ett fel uppstår kommer det att utlösa en `RequestFailedException`.

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

## <a name="entity-linking"></a>Länkning av entitet

Skapa en ny funktion som `EntityLinkingExample()` anropar den klient som du skapade tidigare, anropa sin `RecognizeLinkedEntities()` funktion och iterera genom resultaten. Den returnerade `Response<IReadOnlyCollection<LinkedEntity>>` representerar listan över identifierade entiteter. Om ett fel uppstår kommer det att utlösa en `RequestFailedException`. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista med `LinkedEntityMatch` objekt.

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

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

> [!NOTE]
> I version 2,1 ingår enhets länkning i NER-svaret.

Skapa en ny funktion som `RecognizeEntitiesExample()` anropar den klient som du skapade tidigare och anropa dess [entiteter ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) -funktion. Upprepa resultaten. Det returnerade [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) -objektet kommer att innehålla en lista över `Entities` identifierade entiteter i `errorMessage` om det lyckas, och en om inte. För varje identifierad entitet skriver du ut dess typ, undertyp, Wikipedia-namn (om de finns) samt platserna i den ursprungliga texten.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

Skapa en ny funktion som `KeyPhraseExtractionExample()` anropar den klient som du skapade tidigare och anropa dess `ExtractKeyPhrases()` funktion. Det returnerade `<Response<IReadOnlyCollection<string>>` objektet kommer att innehålla en lista över identifierade nyckel fraser. Om ett fel uppstår kommer det att utlösa en `RequestFailedException`.

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

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

Skapa en ny funktion som `KeyPhraseExtractionExample()` anropar den klient som du skapade tidigare och anropar dess funktions [fraser ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Resultatet kommer att innehålla en lista över identifierade nyckel fraser `KeyPhrases` i om det lyckas, `errorMessage` och en om inte. Skriv ut alla identifierade nyckel fraser.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Resultat

```console
Key phrases:
    cat
    veterinarian
```

---
