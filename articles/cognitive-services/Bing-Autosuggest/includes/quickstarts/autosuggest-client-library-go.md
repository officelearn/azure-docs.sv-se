---
title: Snabb start för klient bibliotek Automatiska förslag i Bing go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: aahi
ms.openlocfilehash: b352e785673d7c4ed3a9b346758ef0d1fa68b36d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887545"
---
Kom igång med Automatiska förslag i Bing klient biblioteket för go. Följ de här stegen för att installera biblioteket och prova våra exempel för grundläggande uppgifter. 

Använd Automatiska förslag i Bing klient bibliotek för gå till Hämta Sök förslag baserat på ofullständiga frågesträngar.

[Referens dokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [bibliotek käll kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [exempel kod](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den senaste versionen av [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-an-azure-resource"></a>Skapa en Azure-resurs 

Börja använda Automatiska förslag i Bing klient biblioteket genom att skapa en Azure-resurs. Välj den resurs typ som passar dig bäst:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Skapa en miljö variabel

>[!NOTE]
> Slut punkterna för icke-testresurser som skapats efter den 1 juli 2019 använder det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Med din nyckel och slut punkt från den resurs som du har skapat skapar du två miljövariabler för autentisering:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`– Resurs nyckeln för autentisering av dina begär Anden.
* `AUTOSUGGEST_ENDPOINT`– Resurs slut punkten för att skicka API-begäranden. Det kommer att se ut så här: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Följ anvisningarna för ditt operativ system.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Starta om konsol fönstret när du har lagt till miljövariabeln.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[macOS](#tab/unix)

Redigera din `.bash_profile`och Lägg till miljövariabeln:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.
***

### <a name="create-a-new-go-project"></a>Skapa ett nytt go-projekt

I ett konsol fönster (cmd, PowerShell, Terminal, bash) skapar du en ny arbets yta för ditt go-projekt och navigerar till den. Arbets ytan kommer att innehålla tre mappar: 

* **src** – den här katalogen innehåller käll kod och paket. Alla paket som installeras med `go get` kommandot kommer att finnas här.
* **pkg** – den här katalogen innehåller de kompilerade go-paket-objekten. De här filerna har ett `.a` fil namns tillägg.
* **bin** – den här katalogen innehåller de binära körbara filer som skapas när `go install`du kör.

> [!TIP]
> Lär dig mer om strukturen för en [Go-arbetsyta](https://golang.org/doc/code.html#Workspaces). Den här guiden innehåller information om `$GOPATH` hur `$GOROOT`du ställer in och.

Nu ska vi skapa en arbets `my-app` yta med namnet och de under `src`kataloger `pkg`som krävs `bin`för,, och:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Installera klient biblioteket för go

Nu ska vi installera klient biblioteket för Go: 

```bash
$ go get -u <library-location-or-url>
```

eller i din lagringsplatskörning om du använder dep:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Skapa ditt Go-program

Nu ska vi skapa en fil med namnet `src/sample-app.go`:

```bash
$ cd src
$ touch sample-app.go
```

Öppna `sample-app.go` och Lägg till paket namnet och importera följande bibliotek:

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

Skapa en funktion med `main`namnet. Skapa sedan miljövariabler för din Automatiska förslag i Bing nyckel och slut punkt.

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

Dessa kod exempel visar hur du utför grundläggande aktiviteter med hjälp av Automatiska förslag i Bing klient bibliotek för Go:

* [Autentisera klienten](#authenticate-the-client)
* [Skicka en API-begäran](#send-an-api-request)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE] 
> Den här snabb starten förutsätter att du har [skapat en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för din automatiska förslags nyckel för Bing, med `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`namnet `BING_AUTOSUGGEST_ENDPOINT`och en för din slut punkt med namnet.

I `main()` funktionen instansierar du en klient med din slut punkt och nyckel. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Skicka en API-begäran

I samma metod använder du klientens [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) -Metod för att skicka en fråga till Bing. Iterera sedan över [förslags](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) svaret och skriv ut det första förslaget.

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

Kör programmet Go med `go run [arguments]` kommandot från program katalogen.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie för automatiska förslag i Bing](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Se även

- [Vad är Automatiska förslag i Bing?](../../get-suggested-search-terms.md)
- [Referens för API v7 för automatiska förslag i Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
