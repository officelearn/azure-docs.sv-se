---
title: 'Snabbstart: Textanalys klient bibliotek för .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att börja använda API för textanalys från Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: assafi
ms.openlocfilehash: 6e6f1d5cfe1f5e745e6f780b5cb9f979520a1f91
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70134917"
---
# <a name="quickstart-text-analytics-client-library-for-net"></a>Snabbstart: Klient bibliotek för text analys för .NET
<a name="HOLTop"></a>

Kom igång med Textanalys klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. 

Använd Textanalys klient bibliotek för .NET för att utföra:

* Sentimentanalys
* Språkidentifiering
* Enhets igenkänning
* Extrahering av nyckelfraser

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE] 
> Demo koden i den här artikeln använder de synkrona metoderna i Textanalys .NET SDK för enkelhetens skull. För produktions scenarier rekommenderar vi dock att du använder batch-asynkrona metoder i dina egna program för att hålla dem skalbara och tillgängliga. Till exempel anropar [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) i stället för [sentiment ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-text-analytics-azure-resource"></a>Skapa en Textanalys Azure-resurs

Få en nyckel för att autentisera dina program med Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Textanalys med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md) eller [Azure CLI](../../cognitive-services-apis-create-account-cli.md) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i 7 dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/)

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, `TEXT_ANALYTICS_SUBSCRIPTION_KEY`med namnet.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C# program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE. 

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet. `text-analytics quickstart` Det här kommandot skapar ett enkelt "Hello World C# "-projekt med en enda käll fil: *program.cs*. 

```console
dotnet new console -n text-analytics-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Från projekt katalogen öppnar du *program.cs* -filen i önskat redigerings program eller IDE. Lägg till följande `using` direktiv:

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

I programmets `Program` klass skapar du variabler för resursens Azure-slutpunkt och prenumerations nyckel. Hämta värdena från miljövariablerna `TEXT_ANALYTICS_SUBSCRIPTION_KEY` i en statisk konstruktor och. `TEXT_ANALYTICS_ENDPOINT` Om du har skapat dessa miljövariabler när du började redigera programmet måste du stänga och öppna den redigerare, IDE eller Shell som du använder för att få åtkomst till variablerna.

```csharp
private const string key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
```

I programmets `Main` metod skapar du autentiseringsuppgifter för att komma åt textanalys-slutpunkten.  Du definierar de metoder som anropas av `Main` metoden senare.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```csharp
static void Main(string[] args)
{
    var credentials = new ApiKeyServiceClientCredentials(subscriptionKey);
    TextAnalyticsClient client = new TextAnalyticsClient(credentials)
    {
        Endpoint = endpoint
    };

    Console.OutputEncoding = System.Text.Encoding.UTF8;
    SentimentAnalysisExample(client);
    // languageDetectionExample(client);
    // entityRecognitionExample(client);
    // KeyPhraseExtractionExample(client);
    
    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du Textanalys klient biblioteket för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.TextAnalytics --version 4.0.0
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) -objekt som autentiserar till Azure med hjälp av din nyckel och ger funktioner för att acceptera text som enkla strängar eller som en batch. Du kan skicka text till API: et synkront eller asynkront. Objektet Response kommer att innehålla analys informationen för varje dokument som du skickar. 


## <a name="code-examples"></a>Kod exempel

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Enhets igenkänning](#entity-recognition)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en ny `ApiKeyServiceClientCredentials` klass för att lagra autentiseringsuppgifterna och lägga till dem i klientens begär Anden. I den skapar du en åsidosättning för `ProcessHttpRequestAsync()` som lägger till din nyckel `Ocp-Apim-Subscription-Key` i rubriken.

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

I- `main()` metoden instansierar du klienten med din nyckel och slut punkt.

```csharp
var credentials = new ApiKeyServiceClientCredentials(key);
TextAnalyticsClient client = new TextAnalyticsClient(credentials)
{
    Endpoint = endpoint
};
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en ny funktion som `SentimentAnalysisExample()` anropar den klient som du skapade tidigare och anropa dess [sentiment ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) funktion. Det returnerade [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) -objektet kommer att `Score` innehålla sentiment om det lyckas `errorMessage` , och en om inte. 

En poäng som är nära 0 anger ett negativt sentiment, medan poängen är närmare 1 betyder en positiv sentiment.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client){
    var result = client.Sentiment("I had the best day of my life.", "en");
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
}
```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Språkidentifiering

Skapa en ny funktion som `languageDetectionExample()` anropar den klient som du skapade tidigare och anropa dess [DetectLanguage ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) funktion. Det returnerade [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) -objektet kommer att innehålla en lista över `DetectedLanguages` identifierade språk i om `errorMessage` det lyckas, och en om inte.  Skriv ut det första returnerade språket.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda `countryHint` -parametern för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange värdet `countryHint = ""` till en tom sträng.

```csharp
static void languageDetectionExample(TextAnalyticsClient client){
    var result = client.DetectLanguage("This is a document written in English.");
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
}
```
<!--
[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

### <a name="output"></a>Output

```console
Language: English
```

## <a name="entity-recognition"></a>Enhets igenkänning

Skapa en ny funktion som `RecognizeEntitiesExample()` anropar den klient som du skapade tidigare och anropa dess [entiteter ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) -funktion. Upprepa resultaten. Det returnerade [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) -objektet kommer att innehålla en lista över `Entities` identifierade entiteter i `errorMessage` om det lyckas, och en om inte. För varje identifierad entitet skriver du ut dess typ, undertyp, Wikipedia-namn (om de finns) samt platserna i den ursprungliga texten.

```csharp
static void entityRecognitionExample(TextAnalyticsClient client){

    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities){
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches){
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
}
```
<!--
[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

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

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en ny funktion som `KeyPhraseExtractionExample()` anropar den klient som du skapade tidigare och anropar dess funktions [fraser ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Resultatet kommer att innehålla en lista över identifierade nyckel fraser `KeyPhrases` i om det lyckas, `errorMessage` och en om inte. Skriv ut alla identifierade nyckel fraser.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är associerade med resurs gruppen.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Översikt över Textanalys](../overview.md)
* [Sentiment-analys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Enhets igenkänning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språk igenkänning](../how-tos/text-analytics-how-to-language-detection.md)
