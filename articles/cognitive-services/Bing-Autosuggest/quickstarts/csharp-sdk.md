---
title: 'Snabbstart: Klientbiblioteket för Automatiska Bing-förslag för .NET | Microsoft-dokument'
description: Kom igång med klientbiblioteket För automatiska förslag på Bing för .NET och få sökförslag baserat på partiella frågesträngar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/20/2019
ms.author: aahi
ms.openlocfilehash: 6bcd89422188d8c0064547c37b4d425f47dd6496
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75473929"
---
# <a name="quickstart-bing-autosuggest-client-library-for-net"></a>Snabbstart: Klientbiblioteket för Automatiska Bing-förslag för .NET

Kom igång med klientbiblioteket för Automatiska förslag på Bing för .NET. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Använd klientbiblioteket För automatiska förslag på Bing för .NET för att hämta sökförslag baserat på partiella frågesträngar.

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [Exempelkod](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Inrätta

### <a name="create-an-azure-resource"></a>Skapa en Azure-resurs

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Skapa en miljövariabel

>[!NOTE]
> Slutpunkterna för icke-utvärderingsresurser som skapats efter den 1 juli 2019 använder det anpassade underdomänformatet som visas nedan. Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Med hjälp av nyckeln och slutpunkten från resursen du skapade skapar du två miljövariabler för autentisering:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`- Resursnyckeln för att autentisera dina begäranden.
* `AUTOSUGGEST_ENDPOINT`- Resursslutpunkten för att skicka API-begäranden. Det kommer att se ut så här: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Följ instruktionerna för operativsystemet.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

När du har lagt till miljövariabeln startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[Macos](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.
***

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskad redigerare eller IDE. 

I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du `bing-autosuggest-quickstart`kommandot för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *program.cs*. 

```console
dotnet new console -n bing-autosuggest-quickstart
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

```console
dotnet build
```

Byggutdata bör inte innehålla några varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Öppna den *program.cs* filen i önskad redigerare eller IDE från projektkatalogen. Lägg till `using` följande direktiv:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

Skapa `Program` variabler för resursens Azure-slutpunkt och -nyckel i klassen. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

I programmets `Main` metod lägger du till följande metodanrop, som du definierar senare.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera klientbiblioteket för Automatiska förslag på Bing i programkatalogen för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket För automatiska förslag på Bing för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Skicka en begäran om automatisk förslag](#send-an-autosuggest-request)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att du har [skapat en miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln För automatiska uggförslag på Bing med namnet `AUTOSUGGEST_SUBSCRIPTION_KEY`och en för slutpunkten `AUTOSUGGEST_ENDPOINT`.


I en ny asynkron metod instansierar du en klient med slutpunkten och nyckeln. Skapa ett [ApiKeyServiceClientCredentials-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) med nyckeln och använd det med slutpunkten för att skapa ett [AutosuggestClient-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet)

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

## <a name="send-an-autosuggest-request"></a>Skicka en begäran om automatisk förslag

I samma metod använder du klientens [autosuggestMethodAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) för att skicka en fråga till Bing. Sedan iterera över [förslag](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) svar, och skriva ut det första förslaget.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>Köra appen

Kör programmet från programkatalogen `dotnet run` med kommandot.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie för automatiska förslag i Bing](../tutorials/autosuggest.md)

## <a name="see-also"></a>Se även

- [Vad är Automatiska förslag i Bing?](../get-suggested-search-terms.md)
- [Punktnätsreferens för automatisk förslag på Bing](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
