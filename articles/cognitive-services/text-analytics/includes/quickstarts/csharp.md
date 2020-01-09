---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: ea526648b1b37919eb41953937d3afa72f7f39e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446286"
---
<a name="HOLTop"></a>

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Library Source Code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Package (NuGet) | -](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) [exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE]
> Koden i den här artikeln använder de synkrona metoderna i Textanalys .NET SDK för enkelhetens skull. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Till exempel anropar [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) i stället för [sentiment ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-text-analytics-azure-resource"></a>Skapa en Textanalys Azure-resurs

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

Skapa en ny .NET Core-konsol med hjälp av Visual Studio IDE. Ett enkelt "Hello World"-projekt skapas med en enda C# källfil: *program.cs*.

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

## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -objekt som autentiserar till Azure med hjälp av din nyckel och ger funktioner för att acceptera text som enkla strängar eller som en batch. Du kan skicka text till API: et synkront eller asynkront. Objektet Response kommer att innehålla analys informationen för varje dokument som du skickar. 

## <a name="code-examples"></a>Kodexempel

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Enhets igenkänning](#entity-recognition)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en ny `ApiKeyServiceClientCredentials`-klass för att lagra autentiseringsuppgifterna och lägga till dem i klientens begär Anden. I den skapar du en åsidosättning för `ProcessHttpRequestAsync()` som lägger till din nyckel i `Ocp-Apim-Subscription-Key`s rubriken.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Skapa en metod för att instansiera [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -objektet med din slut punkt och ett `ApiKeyServiceClientCredentials` objekt som innehåller din nyckel.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

Anropa autentiseringsmetoden i programmets `main()` metod för att instansiera klienten.

## <a name="sentiment-analysis"></a>Känsloanalys

Skapa en ny funktion med namnet `SentimentAnalysisExample()` som tar klienten som du skapade tidigare och anropa sin [sentiment ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) funktion. Det returnerade [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) -objektet kommer att innehålla sentiment `Score` om det lyckas, och en `errorMessage` om inte. 

En poäng som är nära 0 anger ett negativt sentiment, medan poängen är närmare 1 betyder en positiv sentiment.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

### <a name="output"></a>Resultat

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Språkspårning

Skapa en ny funktion med namnet `languageDetectionExample()` som tar klienten som du skapade tidigare och anropa sin [DetectLanguage ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) funktion. Det returnerade [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) -objektet kommer att innehålla en lista över identifierade språk i `DetectedLanguages` om det lyckades, och en `errorMessage` om inte.  Skriv ut det första returnerade språket.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda parametern `countryHint` för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange det här värdet till tom sträng `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Resultat

```console
Language: English
```

## <a name="entity-recognition"></a>Enhetsidentifiering

Skapa en ny funktion med namnet `RecognizeEntitiesExample()` som tar klienten som du skapade tidigare och anropa dess [entiteter ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) -funktion. Upprepa resultaten. Det returnerade [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) -objektet kommer att innehålla en lista över identifierade entiteter i `Entities` om det lyckades och en `errorMessage` om inte. För varje identifierad entitet skriver du ut dess typ, undertyp, Wikipedia-namn (om de finns) samt platserna i den ursprungliga texten.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]


### <a name="output"></a>Resultat

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

## <a name="key-phrase-extraction"></a>Extraktion av nyckelfraser

Skapa en ny funktion som kallas `KeyPhraseExtractionExample()` som tar klienten som du skapade tidigare och anropar dess funktions [fraser ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Resultatet kommer att innehålla en lista över identifierade nyckel fraser i `KeyPhrases` om det lyckas, och en `errorMessage` om inte. Skriv ut alla identifierade nyckel fraser.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Resultat

```console
Key phrases:
    cat
    veterinarian
```
