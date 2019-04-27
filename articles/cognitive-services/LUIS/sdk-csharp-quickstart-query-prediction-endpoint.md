---
title: 'Snabbstart: C# SDK-slutpunkt för förutsägelsefråga'
titleSuffix: Azure Cognitive Services
description: Använd C# SDK till att skicka talindata för användaren till LUIS och ta emot en förutsägelse.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: diberry
ms.openlocfilehash: 086f55094474d4c06e52001d77630932cd04213c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557434"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Snabbstart: Slutpunkt för förutsägelsefråga med C# .NET SDK

Använd .NET SDK, som finns i [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), till att skicka talindata för användaren till LUIS (Language Understanding) och ta emot en förutsägelse av användarens avsikt. 

Den här snabbstarten skickar talindata för användaren, som t.ex. `turn on the bedroom light`, till ett offentligt Language Understanding-program. Därefter tas förutsägelsen emot och visar den främsta avsikten `HomeAutomation.TurnOn` och entiteten `HomeAutomation.Room` i talindatan. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Programmeringsspråket C# (ingår i VS Community 2017)
* Offentligt app-ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> Den fullständiga lösningen är tillgänglig från GitHub-lagringsplatsen [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime).

Letar du efter mer dokumentation?

 * [Referensdokumentation för SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Hämta Cognitive Services- eller Language Understanding-nyckeln

För att kunna använda den offentliga appen vid hemautomatisering, behöver du en giltig nyckel för slutpunktens förutsägelser. Du kan använda antingen en Cognitive Services-nyckel (som skapas nedan med Azure CLI) och är giltig för många kognitiva tjänster, eller en `Language Understanding`-nyckel. 

Använd följande [Azure CLI-kommando till att skapa en nyckel för Cognitive Service](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Skapa ett .NET Core-projekt

Skapa ett .NET Core-konsolprojekt i Visual Studio Community 2017.

1. Öppna Visual Studio Community 2017.
1. Skapa ett nytt projekt genom att från avsnittet **Visual C#** välja **Konsolapp (.NET Core)**.
1. Ange projektnamnet `QueryPrediction`, lämna kvar återstående standardvärden och välj **OK**.
    Detta skapar ett enkelt projekt med en primär kodfil med namnet **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Lägga till SDK med NuGet

1. I **Solution Explorer** väljer du projektet i trädvyn med namnet **QueryPrediction**. Högerklicka sedan. På samma meny väljer du **Hantera NuGet-paket...** .
1. Välj **Bläddra** och ange `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. När paketinformationen visas väljer du **Installera** för att installera paketet i projektet. 
1. Lägg till följande _using_-instruktioner överst i **Program.cs**. Ta inte bort den befintliga _using_-instruktionen för `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Skapa en ny metod för förutsägelsen

Skapa den nya metoden `GetPrediction` som skickar frågan till slutpunkten för förutsägelsefrågan. Metoden skapar och konfigurerar alla nödvändiga objekt och returnerar sedan en `Task` med förutsägelseresultatet [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet). 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Skapa autentiseringsobjekt

Lägg till följande kod i `GetPrediction`-metoden för att skapa klientens autentiseringsuppgifter med din Cognitive Service-nyckel.

Ersätt `<REPLACE-WITH-YOUR-KEY>` med din Cognitive Service-nyckels region. Nyckeln finns i [Azure-portalen](https://portal.azure.com) på nyckelsidan för resursen.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Skapa en LUIS-klient

I `GetPrediction`-metoden, efter föregående kod, lägger du till följande kod för att använda de nya autentiseringsuppgifterna och skapa ett [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___)-klientobjekt. 

Ersätt `<REPLACE-WITH-YOUR-KEY-REGION>` med din nyckelregion, till exempel `westus`. Nyckelregionen finns i [Azure-portalen](https://portal.azure.com) på översiktssidan för resursen.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Ange frågeparametrar

I `GetPrediction`-metoden efter föregående kod lägger du till följande kod för att ange frågeparametrarna.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Slutpunkt för frågeförutsägelse

I `GetPrediction`-metoden efter föregående kod lägger du till följande kod för att ange frågeparametrarna:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Visa förutsägelseresultat

Ändra **Main**-metoden för att anropa den nya `GetPrediction`-metoden och returnera sedan förutsägelseresultatet:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Kör projektet

Skapa projektet i Studio och kör projektet för att se resultatet av frågan:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) och [referensdokumentationen för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Självstudie: Skapa en LUIS-app som identifierar användarens avsikter](luis-quickstart-intents-only.md) 