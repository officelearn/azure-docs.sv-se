---
title: 'Snabb start: Textanalys klient bibliotek C# för | Microsoft Docs'
description: Kom igång med Textanalys klient biblioteket förC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: a907fb5347e6559e066f678c53bb16c7727a74b5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987940"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

[v3 referens dokumentation](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3-paket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3-exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

[v2 referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 bibliotek käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2-paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

Skapa en ny .NET Core-konsol med hjälp av Visual Studio IDE. Då skapas ett "Hello World"-projekt med en enda C# källfil: *program.cs*.

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Installera klient biblioteket genom att högerklicka på lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I paket hanteraren som öppnas väljer du **Bläddra**, markerar **ta med för hands version**och söker efter `Azure.AI.TextAnalytics`. Klicka på den och **Installera**sedan. Du kan också använda [Package Manager-konsolen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Öppna filen *program.cs* och Lägg till följande `using` direktiv:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

I programmets `Program` klass skapar du variabler för resursens nyckel och slut punkt.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Ersätt programmets `Main` metod. Du definierar de metoder som anropas här senare.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);
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

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Installera klient biblioteket genom att högerklicka på lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I den paket hanterare som öppnas väljer du **Bläddra** och söker efter `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klicka på den och **Installera**sedan. Du kan också använda [Package Manager-konsolen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

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

Textanalys-klienten är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med hjälp av din nyckel och ger funktioner för att acceptera text som enkla strängar eller som en batch. Du kan skicka text till API: et synkront eller asynkront. Objektet Response kommer att innehålla analys informationen för varje dokument som du skickar. 

Om du använder version `3.0-preview`kan du använda en valfri `TextAnalyticsClientOptions`-instans för att initiera klienten med olika standardinställningar (till exempel standard språk eller lands tips). Du kan också autentisera med hjälp av en Azure Active Directory-token. 

## <a name="code-examples"></a>Kod exempel

* [Sentiment-analys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner)
* [Länkning av entitet](#entity-linking)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Kontrol lera att din huvud metod från tidigare skapar ett nytt klient objekt med din slut punkt och nyckel. 

```csharp
var client = new TextAnalyticsClient(endpoint, key);
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Skapa en ny `ApiKeyServiceClientCredentials`-klass för att lagra autentiseringsuppgifterna och lägga till dem i klientens begär Anden. I den skapar du en åsidosättning för `ProcessHttpRequestAsync()` som lägger till din nyckel i `Ocp-Apim-Subscription-Key`s rubriken.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Skapa en metod för att instansiera [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -objektet med din slut punkt och ett `ApiKeyServiceClientCredentials` objekt som innehåller din nyckel.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Känsloanalys

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Skapa en ny funktion som kallas `SentimentAnalysisExample()` som tar klienten som du skapade tidigare och anropar dess `AnalyzeSentiment()`-funktion. Det returnerade `Response<AnalyzeSentimentResult>`-objektet innehåller sentiment-etiketten och poängen i hela indatamängden, samt en sentiment-analys för varje mening om det lyckas, och en `Value.ErrorMessage` om inte.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Resultat

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Skapa en ny funktion med namnet `SentimentAnalysisExample()` som tar klienten som du skapade tidigare och anropa sin [sentiment ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) funktion. Det returnerade [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) -objektet kommer att innehålla sentiment `Score` om det lyckas, och en `errorMessage` om inte. 

En poäng som är nära 0 anger ett negativt sentiment, medan poängen är närmare 1 betyder en positiv sentiment.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Språkspårning

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)


Skapa en ny funktion som kallas `LanguageDetectionExample()` som tar klienten som du skapade tidigare och anropar dess `DetectLanguage()`-funktion. Det returnerade `Response<DetectLanguageResult>`-objektet kommer att innehålla det identifierade språket i `Value.PrimaryLanguage` om det lyckas, och en `Value.ErrorMessage` om inte.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda parametern `countryHint` för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange det här värdet till tom sträng `countryHint = ""`. Ange ett annat standardvärde genom att ange egenskapen `TextAnalyticsClientOptions.DefaultCountryHint` och skicka den under klient initieringen.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Resultat

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Skapa en ny funktion med namnet `languageDetectionExample()` som tar klienten som du skapade tidigare och anropa sin [DetectLanguage ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) funktion. Det returnerade [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) -objektet kommer att innehålla en lista över identifierade språk i `DetectedLanguages` om det lyckades, och en `errorMessage` om inte.  Skriv ut det första returnerade språket.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda parametern `countryHint` för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange det här värdet till tom sträng `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Resultat

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)


> [!NOTE]
> I version `3.0-preview`:
> * NER innehåller separata metoder för att identifiera personlig information. 
> * Enhets länkning är en separat begäran än NER.

Koden nedan är för att identifiera personlig information med NER v3, som finns i en offentlig för hands version.


Skapa en ny funktion som heter `EntityRecognitionExample()` som tar klienten som du skapade tidigare, anropa dess `RecognizeEntities()`-funktion och iterera genom resultaten. Det returnerade `Response<RecognizeEntitiesResult>`-objektet kommer att innehålla en lista över identifierade entiteter i `Value.NamedEntities` om det lyckades och en `Value.ErrorMessage` om inte. För varje identifierad entitet skriver du ut dess typ och under typ om den finns.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Resultat

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="using-ner-to-detect-personal-information"></a>Använda NER för att identifiera personlig information

Skapa en ny funktion som heter `EntityPIIExample()` som tar klienten som du skapade tidigare, anropa dess `RecognizePiiEntities()`-funktion och iterera genom resultaten. Precis som den föregående funktionen returnerade `Response<RecognizeEntitiesResult>`-objektet kommer att innehålla listan över identifierade entiteter i `Value.NamedEntities` om det lyckades och en `Value.ErrorMessage` om inte.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Resultat

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Länkning av entitet

Skapa en ny funktion som heter `EntityLinkingExample()` som tar klienten som du skapade tidigare, anropa dess `RecognizeLinkedEntities()`-funktion och iterera genom resultaten. Det returnerade `Response<RecognizeLinkedEntitiesResult>`-objektet kommer att innehålla en lista över identifierade entiteter i `Value.LinkedEntities` om det lyckades och en `Value.ErrorMessage` om inte. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista över `LinkedEntityMatch` objekt.

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
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
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
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

> [!NOTE]
> I version 2,1 ingår enhets länkning i NER-svaret.

Skapa en ny funktion med namnet `RecognizeEntitiesExample()` som tar klienten som du skapade tidigare och anropa dess [entiteter ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) -funktion. Upprepa resultaten. Det returnerade [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) -objektet kommer att innehålla en lista över identifierade entiteter i `Entities` om det lyckades och en `errorMessage` om inte. För varje identifierad entitet skriver du ut dess typ, undertyp, Wikipedia-namn (om de finns) samt platserna i den ursprungliga texten.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extraktion av nyckelfraser

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Skapa en ny funktion som kallas `KeyPhraseExtractionExample()` som tar klienten som du skapade tidigare och anropar dess `ExtractKeyPhrases()`-funktion. Resultatet kommer att innehålla en lista över identifierade nyckel fraser i `Value.KeyPhrases` om det lyckas, och en `Value.ErrorMessage` om inte. Skriv ut alla identifierade nyckel fraser.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
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

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Skapa en ny funktion som kallas `KeyPhraseExtractionExample()` som tar klienten som du skapade tidigare och anropar dess funktions [fraser ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Resultatet kommer att innehålla en lista över identifierade nyckel fraser i `KeyPhrases` om det lyckas, och en `errorMessage` om inte. Skriv ut alla identifierade nyckel fraser.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Resultat

```console
Key phrases:
    cat
    veterinarian
```

---