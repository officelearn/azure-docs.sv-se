---
title: 'Snabbstart: Text Analytics klientbibliotek för Go | Microsoft-dokument'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten identifierar du språk med hjälp av go textanalysklientbiblioteket från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0b4495616c750b2b3e8431e011d71ae8671af1ef
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77912656"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Snabbstart: Använda klientbiblioteket Text Analytics for Go

[Referensdokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Biblioteksnamnkodpaket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [(GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Den här snabbstarten gäller endast textanalys version 2.1. För närvarande är ett v3-klientbibliotek för Go inte tillgängligt.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en gratis](https://azure.microsoft.com/free/)
* Den senaste versionen av [Go](https://golang.org/dl/)
* När du har din <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-prenumeration"  target="_blank">skapar Skapa en <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics-resurs en Text Analytics-resurs i Azure-portalen för att hämta din nyckel och slutpunkt. 
    * Du behöver nyckeln och slutpunkten från den resurs du skapar för att ansluta ditt program till Text Analytics API. Du gör detta senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-go-project"></a>Skapa ett nytt Go-projekt

Skapa en ny arbetsyta för Go-projektet i ett konsolfönster (cmd, PowerShell, Terminal, Bash) och navigera till den. Arbetsytan innehåller tre mappar: 

* **src** - Den här katalogen innehåller källkod och paket. Alla paket som `go get` installeras med kommandot finns här.
* **pkg** - Den här katalogen innehåller kompilerade Go-paketobjekt. Dessa filer har `.a` alla ett tillägg.
* **bin** - Den här katalogen innehåller de binära `go install`körbara filer som skapas när du kör .

> [!TIP]
> Läs mer om strukturen på en [Go-arbetsyta](https://golang.org/doc/code.html#Workspaces). Den här guiden `$GOPATH` innehåller `$GOROOT`information för inställning och .

Skapa en arbetsyta `my-app` som anropas och `src` `pkg`de `bin`underkataloger som krävs för , och :

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Installera klientbiblioteket Text Analytics for Go

Installera klientbiblioteket för Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

eller i din lagringsplatskörning om du använder dep:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Skapa ditt Go-program

Skapa sedan en `src/quickstart.go`fil med namnet:

```bash
$ cd src
$ touch quickstart.go
```

Öppna `quickstart.go` i din favorit-IDE- eller textredigerare. Lägg sedan till paketnamnet och importera följande bibliotek:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Objektmodell 

Text Analytics-klienten är ett [BaseClient-objekt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) som autentiserar till Azure med hjälp av din nyckel. Klienten innehåller flera metoder för att analysera text, som en enda sträng eller en batch. 

Text skickas till API:et `documents`som `dictionary` en lista över `id` `text`, `language` som är objekt som innehåller en kombination av , och attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprunget `language`och `id` kan vara valfritt värde. 

Svarsobjektet är en lista som innehåller analysinformationen för varje dokument. 

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med Text Analytics-klientbiblioteket för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språkidentifiering](#language-detection)
* [Erkännande av entitet](#entity-recognition)
* [Extraktion av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten


Skapa variabler för resursens Azure-slutpunkt och prenumerationsnyckel i en ny funktion.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Skapa ett nytt [BaseClient-objekt.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) Skicka din nyckel till [autorestet. Funktionen NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) som sedan skickas till klientens `authorizer` egenskap.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en ny `SentimentAnalysis()` funktion som anropas och skapa en klient med den `GetTextAnalyticsClient()` metod som skapats tidigare. Skapa en lista över [MultiLanguageInput-objekt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) som innehåller de dokument som du vill analysera. Varje objekt innehåller `id` `Language` ett `text` och ett attribut. Attributet `text` lagrar texten som ska `language` analyseras, är dokumentets `id` språk och kan vara valfritt värde. 

Anropa klientens [Sentiment()-funktion](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) och få resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID och sentiment poäng. En poäng närmare 0 indikerar en negativ känsla, medan en poäng närmare 1 indikerar en positiv känsla.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

samtal `SentimentAnalysis()` i ditt projekt.

### <a name="output"></a>Resultat

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Språkidentifiering

Skapa en ny `LanguageDetection()` funktion som anropas och skapa en klient med den `GetTextAnalyticsClient()` metod som skapats tidigare. Skapa en lista över [LanguageInput-objekt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) som innehåller de dokument som du vill analysera. Varje objekt innehåller `id` ett `text` och ett attribut. Attributet `text` lagrar texten som ska analyseras och kan `id` vara vilket värde som helst. 

Ring klientens [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) och få resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID och upptäckt språk.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Ring `LanguageDetection()` in ditt projekt.

### <a name="output"></a>Resultat

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Enhetsidentifiering

Skapa en ny `ExtractEntities()` funktion som anropas och skapa en klient med den `GetTextAnalyticsClient()` metod som skapats tidigare. Skapa en lista över [MultiLanguageInput-objekt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) som innehåller de dokument som du vill analysera. Varje objekt innehåller `id` `language`ett , `text` och ett attribut. Attributet `text` lagrar texten som ska `language` analyseras, är dokumentets `id` språk och kan vara valfritt värde. 

Anropa klientens [entiteter()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) och få resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID och extraherade entiteter poäng.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

samtal `ExtractEntities()` i ditt projekt.

### <a name="output"></a>Resultat

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en ny `ExtractKeyPhrases()` funktion som anropas och skapa en klient med den `GetTextAnalyticsClient()` metod som skapats tidigare. Skapa en lista över [MultiLanguageInput-objekt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) som innehåller de dokument som du vill analysera. Varje objekt innehåller `id` `language`ett , `text` och ett attribut. Attributet `text` lagrar texten som ska `language` analyseras, är dokumentets `id` språk och kan vara valfritt värde.

Ring klientens [Nyckelfraser()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) och få resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID och extraherade nyckelfraser.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Ring `ExtractKeyPhrases()` in ditt projekt.

### <a name="output"></a>Resultat

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
