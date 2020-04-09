---
title: Snabbstart för snabbstart för Bing Autosuggest Go-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: aahi
ms.openlocfilehash: b352e785673d7c4ed3a9b346758ef0d1fa68b36d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887545"
---
Kom igång med klientbiblioteket För Start för Bing-kort. Följ dessa steg för att installera biblioteket och prova våra exempel för grundläggande uppgifter. 

Använd klientbiblioteket För automatiska förslag på Bing för Go för att få sökförslag baserat på partiella frågesträngar.

[Exempelkod för referensdokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [Bibliotekskod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Sample code](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en gratis](https://azure.microsoft.com/free/)
* Den senaste versionen av [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Inrätta

### <a name="create-an-azure-resource"></a>Skapa en Azure-resurs 

Börja använda klientbiblioteket för automatiska förslag på Bing genom att skapa en Azure-resurs. Välj den resurstyp nedan som är rätt för dig:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

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
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

När du har lagt till miljövariabeln startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[macOS](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.
***

### <a name="create-a-new-go-project"></a>Skapa ett nytt Go-projekt

Skapa en ny arbetsyta för Go-projektet i ett konsolfönster (cmd, PowerShell, Terminal, Bash) och navigera till den. Arbetsytan innehåller tre mappar: 

* **src** - Den här katalogen innehåller källkod och paket. Alla paket som `go get` installeras med kommandot finns här.
* **pkg** - Den här katalogen innehåller kompilerade Go-paketobjekt. Dessa filer har `.a` alla ett tillägg.
* **bin** - Den här katalogen innehåller de binära `go install`körbara filer som skapas när du kör .

> [!TIP]
> Läs mer om strukturen på en [Go-arbetsyta](https://golang.org/doc/code.html#Workspaces). Den här guiden `$GOPATH` innehåller `$GOROOT`information för inställning och .

Låt oss skapa en `my-app` arbetsyta som heter och `src` `pkg`de `bin`subkataloger som krävs för , och:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Installera klientbiblioteket för Go

Nu ska vi installera klientbiblioteket för Go: 

```bash
$ go get -u <library-location-or-url>
```

eller i din lagringsplatskörning om du använder dep:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Skapa ditt Go-program

Nu ska vi skapa en `src/sample-app.go`fil med namnet:

```bash
$ cd src
$ touch sample-app.go
```

Öppna `sample-app.go` och lägg till paketnamnet och importera följande bibliotek:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Skapa en `main`funktion med namnet . Skapa sedan miljövariabler för nyckeln och slutpunkten för Bing-kortkommando.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Kodexempel

De här kodexemplen visar hur du slutför grundläggande uppgifter med hjälp av klientbiblioteket Bing Autosuggest for Go:

* [Autentisera klienten](#authenticate-the-client)
* [Skicka en API-begäran](#send-an-api-request)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE] 
> Den här snabbstarten förutsätter att du har [skapat en miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för din autosuggestnyckel för Bing, med namnet `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`, och en för slutpunkten med namnet `BING_AUTOSUGGEST_ENDPOINT`.

I `main()` funktionen instansierar du en klient med slutpunkten och nyckeln. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Skicka en API-begäran

I samma metod använder du klientens [autosuggestMethodAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) för att skicka en fråga till Bing. Sedan iterera över [förslag](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) svar, och skriva ut det första förslaget.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Köra appen

Kör go-programmet `go run [arguments]` med kommandot från programkatalogen.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portalen](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie för automatiska förslag i Bing](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Se även

- [Vad är Automatiska förslag i Bing?](../../get-suggested-search-terms.md)
- [Referens för API v7 för automatiska förslag i Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
