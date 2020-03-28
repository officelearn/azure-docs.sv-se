---
title: 'Snabbstart: Text Analytics klientbibliotek för Ruby | Microsoft-dokument'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten identifierar du språk med hjälp av Ruby Text Analytics-klientbiblioteket från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0d4d32a413dd22c55f1b2f01dce3a3df81f5f729
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77919676"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Snabbstart: Använda klientbiblioteket Text Analytics för Ruby

Kom igång med textanalysklientbiblioteket. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Använd textanalysklientbiblioteket för att utföra:

* Sentimentanalys
* Språkidentifiering
* Enhetsidentifiering
* Extrahering av nyckelfraser

> [!NOTE]
> Den här snabbstarten gäller endast textanalys version 2.1. För närvarande är ett v3-klientbibliotek för Ruby inte tillgängligt.

[Referensdokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Biblioteksnamnkodpaket](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [(RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en gratis](https://azure.microsoft.com/free/)
* Den nuvarande versionen av [Ruby](https://www.ruby-lang.org/)
* När du har din <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-prenumeration"  target="_blank">skapar Skapa en <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics-resurs en Text Analytics-resurs i Azure-portalen för att hämta din nyckel och slutpunkt. 
    * Du behöver nyckeln och slutpunkten från den resurs du skapar för att ansluta ditt program till Text Analytics API. Du gör detta senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-ruby-application"></a>Skapa ett nytt Ruby-program

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den. Skapa sedan en `GemFile`fil med namnet och en Ruby-fil för din kod.

```console
mkdir myapp && cd myapp
```

Lägg `GemFile`till följande rader i din för att lägga till klientbiblioteket som ett beroende.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Importera följande paket i Ruby-filen.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Skapa variabler för resursens Azure-slutpunkt och -nyckel. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Objektmodell 

Text Analytics-klienten autentiserar till Azure med hjälp av din nyckel. Klienten innehåller flera metoder för att analysera text, som en enda sträng eller en batch. 

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

Skapa en `TextAnalyticsClient`klass med namnet . 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

I den här klassen `initialize` skapar du en funktion som anropas för att autentisera klienten med hjälp av nyckeln och slutpunkten. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Utanför klassen använder du klientens `new()` funktion för att instansiera den.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en funktion som anropas `AnalyzeSentiment()` i klientobjektet och en lista över indatadokument som skapas senare. Ring klientens `sentiment()` funktion och få resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID och sentiment poäng. En poäng närmare 0 indikerar en negativ känsla, medan en poäng närmare 1 indikerar en positiv känsla.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Utanför klientfunktionen skapar du en `SentimentAnalysisExample()` ny funktion `TextAnalyticsClient` som anropas och som tar objektet som skapats tidigare. Skapa en `MultiLanguageInput` lista med objekt som innehåller de dokument som du vill analysera. Varje objekt innehåller `id` `Language` ett `text` och ett attribut. Attributet `text` lagrar texten som ska `language` analyseras, är dokumentets `id` språk och kan vara valfritt värde. Anropa sedan klientens `AnalyzeSentiment()` funktion.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Anropa funktionen `SentimentAnalysisExample()`.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Resultat

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Språkidentifiering

Skapa en funktion som anropas `DetectLanguage()` i klientobjektet och en lista över indatadokument som skapas senare. Ring klientens `detect_language()` funktion och få resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID och upptäckt språk.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Utanför klientfunktionen skapar du en `DetectLanguageExample()` ny funktion `TextAnalyticsClient` som anropas och som tar objektet som skapats tidigare. Skapa en `LanguageInput` lista med objekt som innehåller de dokument som du vill analysera. Varje objekt innehåller `id`ett `text` och ett attribut. Attributet `text` lagrar texten som ska analyseras och kan `id` vara vilket värde som helst. Anropa sedan klientens `DetectLanguage()` funktion.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Anropa funktionen `DetectLanguageExample()`.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Resultat

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Enhetsidentifiering

Skapa en funktion som anropas `RecognizeEntities()` i klientobjektet och en lista över indatadokument som skapas senare. Ring klientens `entities()` funktion och få resultatet. Sedan iterera genom resultaten och skriva ut varje dokuments ID och de erkända entiteterna.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Utanför klientfunktionen skapar du en `RecognizeEntitiesExample()` ny funktion `TextAnalyticsClient` som anropas och som tar objektet som skapats tidigare. Skapa en `MultiLanguageInput` lista med objekt som innehåller de dokument som du vill analysera. Varje objekt innehåller `id`ett `language`, `text` ett och ett attribut. Attributet `text` lagrar texten som ska `language` analyseras, är språket `id` i texten och kan vara valfritt värde. Anropa sedan klientens `RecognizeEntities()` funktion.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Anropa funktionen `RecognizeEntitiesExample()`.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Resultat

```console
===== ENTITY RECOGNITION =====
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

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en funktion som anropas `ExtractKeyPhrases()` i klientobjektet och en lista över indatadokument som skapas senare. Ring klientens `key_phrases()` funktion och få resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID, och extraherade nyckelfraser.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Utanför klientfunktionen skapar du en `KeyPhraseExtractionExample()` ny funktion `TextAnalyticsClient` som anropas och som tar objektet som skapats tidigare. Skapa en `MultiLanguageInput` lista med objekt som innehåller de dokument som du vill analysera. Varje objekt innehåller `id`ett `language`, `text` ett och ett attribut. Attributet `text` lagrar texten som ska `language` analyseras, är språket `id` i texten och kan vara valfritt värde. Anropa sedan klientens `ExtractKeyPhrases()` funktion.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Anropa funktionen `KeyPhraseExtractionExample()`.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

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
                rock
Document ID: 4
         Key phrases:
                fútbol
```
