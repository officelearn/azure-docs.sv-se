---
title: 'Snabb start: Textanalys klient bibliotek för go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten identifierar du språk genom att använda Go Textanalys klient biblioteket från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/29/2020
ms.author: aahi
ms.openlocfilehash: 5623be028cfe10c0ea6786b24bf75ae2bf9c5255
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560799"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Snabb start: Använd Textanalys klient bibliotek för go

[Referens dokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Library Source Code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Package (GitHub) | -](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) [exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Den här snabb starten gäller endast Textanalys version 2,1. För närvarande är ett v3-klient bibliotek för Go inte tillgängligt.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den senaste versionen av [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-text-analytics-azure-resource"></a>Skapa en Textanalys Azure-resurs 

[!INCLUDE [text-analytics-resource-creation](../includes/quickstarts/resource-creation.md)]

### <a name="create-a-new-go-project"></a>Skapa ett nytt go-projekt

I ett konsol fönster (cmd, PowerShell, Terminal, bash) skapar du en ny arbets yta för ditt go-projekt och navigerar till den. Arbets ytan kommer att innehålla tre mappar: 

* **src** – den här katalogen innehåller käll kod och paket. Alla paket som installeras med kommandot `go get` kommer att finnas här.
* **pkg** – den här katalogen innehåller de kompilerade go-paket-objekten. De här filerna har ett `.a`-tillägg.
* **bin** – den här katalogen innehåller de binära körbara filer som skapas när du kör `go install`.

> [!TIP]
> Lär dig mer om strukturen för en [Go-arbetsyta](https://golang.org/doc/code.html#Workspaces). Den här guiden innehåller information om hur du ställer in `$GOPATH` och `$GOROOT`.

Skapa en arbets yta med namnet `my-app` och de under kataloger som krävs för `src`, `pkg`och `bin`:

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

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Objekt modell 

Textanalys-klienten är ett [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) -objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch. 

Text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination av `id`, `text`och `language` attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprungs `language`och `id` kan vara vilket värde som helst. 

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande med Textanalys klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Enhets igenkänning](#entity-recognition)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten


Skapa variabler för resursens Azure-slut punkt och prenumerations nyckel i en ny funktion.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Skapa ett nytt [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) -objekt. Skicka nyckeln till [AutoRest. NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) -funktionen som skickas sedan till klientens `authorizer`-egenskap.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Attitydanalys

Skapa en ny funktion som kallas `SentimentAnalysis()` och skapa en klient med hjälp av `GetTextAnalyticsClient()`-metoden som skapades tidigare. Skapa en lista med [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att innehålla ett `id`, `Language` och ett `text`-attribut. Attributet `text` lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. 

Anropa klientens [sentiment ()-](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) funktion och hämta resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

anropa `SentimentAnalysis()` i ditt projekt.

### <a name="output"></a>Resultat

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Språkidentifiering

Skapa en ny funktion som kallas `LanguageDetection()` och skapa en klient med hjälp av `GetTextAnalyticsClient()`-metoden som skapades tidigare. Skapa en lista med [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) -objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att innehålla ett `id` och ett `text`-attribut. Attributet `text` lagrar texten som ska analyseras och `id` kan vara vilket värde som helst. 

Anropa klientens [DetectLanguage ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och identifierat språk.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

anropa `LanguageDetection()` i ditt projekt.

### <a name="output"></a>Resultat

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Enhetsidentifiering

Skapa en ny funktion som kallas `ExtractEntities()` och skapa en klient med hjälp av `GetTextAnalyticsClient()`-metoden som skapades tidigare. Skapa en lista med [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att innehålla ett `id`, `language`och ett `text`-attribut. Attributet `text` lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. 

Anropa klientens [entiteter ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och resultat för extraherade enheter.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

anropa `ExtractEntities()` i ditt projekt.

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

Skapa en ny funktion som kallas `ExtractKeyPhrases()` och skapa en klient med hjälp av `GetTextAnalyticsClient()`-metoden som skapades tidigare. Skapa en lista med [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att innehålla ett `id`, `language`och ett `text`-attribut. Attributet `text` lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst.

Anropa klientens diskussions [fraser ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och extraherade nyckel fraser.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

anropa `ExtractKeyPhrases()` i ditt projekt.

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
