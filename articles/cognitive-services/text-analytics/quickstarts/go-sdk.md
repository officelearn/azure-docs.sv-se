---
title: 'Snabbstart: Textanalys klient bibliotek för go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Få information och kod exempel som hjälper dig att snabbt komma igång med API för textanalys i Azure Cognitive Services.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 25d8052cda422c185d49c36c5daff9ac4582e66c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881005"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>Snabbstart: Klient bibliotek för text analys för go

Kom igång med Textanalys klient biblioteket för go. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. 

Använd Textanalys klient bibliotek för att gå till utföra:

* Sentimentanalys
* Språkidentifiering
* Enhets igenkänning
* Extrahering av nyckelfraser

[Referens dokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [paket (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den senaste versionen av [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-text-analytics-azure-resource"></a>Skapa en Textanalys Azure-resurs 

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Textanalys med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i 7 dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Visa din resurs på [Azure Portal](https://portal.azure.com).

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, `TEXT_ANALYTICS_SUBSCRIPTION_KEY`med namnet.

### <a name="create-a-new-go-project"></a>Skapa ett nytt go-projekt

I ett konsol fönster (cmd, PowerShell, Terminal, bash) skapar du en ny arbets yta för ditt go-projekt och navigerar till den. Arbets ytan kommer att innehålla tre mappar: 

* **src** – den här katalogen innehåller käll kod och paket. Alla paket som installeras med `go get` kommandot kommer att finnas här.
* **pkg** – den här katalogen innehåller de kompilerade go-paket-objekten. De här filerna har ett `.a` fil namns tillägg.
* **bin** – den här katalogen innehåller de binära körbara filer som skapas när `go install`du kör.

> [!TIP]
> Lär dig mer om strukturen för en [Go-arbetsyta](https://golang.org/doc/code.html#Workspaces). Den här guiden innehåller information om `$GOPATH` hur `$GOROOT`du ställer in och.

Skapa en arbets yta `my-app` med namnet och de under kataloger `src`som `pkg`krävs för `bin`,, och:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Installera Textanalys klient bibliotek för go

Installera klient biblioteket för Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

eller i din lagringsplatskörning om du använder dep:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Skapa ditt Go-program

Skapa sedan en fil med namnet `src/quickstart.go`:

```bash
$ cd src
$ touch quickstart.go
```

Öppna `quickstart.go` i din favorit-IDE-eller text redigerare. Lägg sedan till paket namnet och importera följande bibliotek:

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
)
```

Lägg till följande funktioner i projektet som de flesta av parametrarna och egenskaperna för den här snabb starten förväntar sig sträng och bool-pekare.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

I projektets huvud funktion skapar du variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
// This sample assumes you have created an environment variable for your key
subscriptionKey := os.Getenv("TEXT_ANALYTICS_SUBSCRIPTION_KEY")
// replace this endpoint with the correct one for your Azure resource. 
endpoint := "https://eastus.api.cognitive.microsoft.com"
```

## <a name="object-model"></a>Objekt modell 

Textanalys-klienten är ett [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) -objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch. 

Text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination `id`av `text`,, `language` och attribut beroende på vilken metod som används. Attributet lagrar texten som ska analyseras i ursprunget `language`och `id` kan vara vilket värde som helst. `text` 

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande med Textanalys klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Enhets igenkänning](#entity-recognition)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa ett nytt [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) -objekt i projektets huvud funktion. Skicka nyckeln till AutoRest [. NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) -funktionen som skickas sedan till klientens `authorizer` egenskap.

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en ny funktion `SentimentAnalysis()` som gör att klienten skapas tidigare. Skapa en lista med [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att `id`innehålla `Language` ett- `text` och-attribut. Attributet lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. `text` 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

I samma funktion anropar du klientens [sentiment ()-](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) funktion och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

```golang
result, _ := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

I projektets huvud funktion anropar `SentimentAnalysis()`du.

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>Språkidentifiering

Skapa en ny funktion `LanguageDetection()` som gör att klienten skapas tidigare. Skapa en lista med [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) -objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att `id` innehålla ett `text` -och-attribut. Attributet lagrar texten som ska analyseras `id` och kan vara vilket värde som helst. `text` 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

I samma funktion anropar du klientens [DetectLanguage ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och identifierat språk.

```golang
result, _ := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

I projektets huvud funktion anropar `LanguageDetection()`du.

### <a name="output"></a>Output

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>Enhets igenkänning

Skapa en ny funktion `ExtractEntities()` som gör att klienten skapas tidigare. Skapa en lista med [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att `id`innehålla `language`ett,- `text` och-attribut. Attributet lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. `text` 

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        }
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

I samma funktion anropar du klientens [entiteter ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) och får resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och resultat för extraherade enheter.

```golang
    result, _ := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

I projektets huvud funktion anropar `ExtractEntities()`du.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en ny funktion `ExtractKeyPhrases()` som gör att klienten skapas tidigare. Skapa en lista med [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att `id`innehålla `language`ett,- `text` och-attribut. Attributet lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. `text`

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

I samma funktion anropar du klientens inloggnings [fraser ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) och får resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och extraherade nyckel fraser.

```golang
    result, _ := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

I projektets huvud funktion anropar `ExtractKeyPhrases()`du.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Key Phrases:
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
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices).
