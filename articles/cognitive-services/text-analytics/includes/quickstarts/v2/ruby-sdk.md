---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/02/2019
ms.author: aahi
ms.openlocfilehash: 637288933037b10aab8c22f7c629e52751057ad6
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281406"
---
[Referens dokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Library Source Code](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [Package (rubygems) | -](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) [exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Aktuell version av [ruby](https://www.ruby-lang.org/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-text-analytics-azure-resource"></a>Skapa en Textanalys Azure-resurs 

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-ruby-application"></a>Skapa ett nytt ruby-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. Skapa sedan en fil med namnet `GemFile`och en ruby-fil för koden.

```console
mkdir myapp && cd myapp
```

I `GemFile`lägger du till följande rader för att lägga till klient biblioteket som ett beroende.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Importera följande paket i ruby-filen.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Skapa variabler för resursens Azure-slutpunkt och nyckel. 

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info-v2.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Objekt modell 

Textanalys klienten autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch. 

Text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination av `id`, `text`och `language` attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprungs `language`och `id` kan vara vilket värde som helst. 

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med Textanalys klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Enhets igenkänning](#entity-recognition)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en klass med namnet `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

I den här klassen skapar du en funktion som kallas `initialize` för att autentisera klienten med hjälp av din nyckel och slut punkt. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Utanför klassen använder du klientens `new()` funktion för att instansiera den.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Känsloanalys

I objektet klient skapar du en funktion som heter `AnalyzeSentiment()` som tar en lista med indatamängds dokument som ska skapas senare. Anropa klientens `sentiment()` funktion och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Utanför klient funktionen skapar du en ny funktion som heter `SentimentAnalysisExample()` som tar `TextAnalyticsClient`-objektet som skapades tidigare. Skapa en lista med `MultiLanguageInput` objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att innehålla ett `id`, `Language` och ett `text`-attribut. Attributet `text` lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. Anropa sedan klientens `AnalyzeSentiment()` funktion.

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

## <a name="language-detection"></a>Språkspårning

I objektet klient skapar du en funktion som heter `DetectLanguage()` som tar en lista med indatamängds dokument som ska skapas senare. Anropa klientens `detect_language()` funktion och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och identifierat språk.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Utanför klient funktionen skapar du en ny funktion som heter `DetectLanguageExample()` som tar `TextAnalyticsClient`-objektet som skapades tidigare. Skapa en lista med `LanguageInput` objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att innehålla ett `id`och ett `text`-attribut. Attributet `text` lagrar texten som ska analyseras och `id` kan vara vilket värde som helst. Anropa sedan klientens `DetectLanguage()` funktion.

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

I objektet klient skapar du en funktion som heter `RecognizeEntities()` som tar en lista med indatamängds dokument som ska skapas senare. Anropa klientens `entities()` funktion och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och de identifierade enheterna.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Utanför klient funktionen skapar du en ny funktion som heter `RecognizeEntitiesExample()` som tar `TextAnalyticsClient`-objektet som skapades tidigare. Skapa en lista med `MultiLanguageInput` objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att innehålla ett `id`, ett `language`och ett `text`-attribut. Attributet `text` lagrar texten som ska analyseras, `language` är språk texten och `id` kan vara vilket värde som helst. Anropa sedan klientens `RecognizeEntities()` funktion.

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

## <a name="key-phrase-extraction"></a>Extraktion av nyckelfraser

I objektet klient skapar du en funktion som heter `ExtractKeyPhrases()` som tar en lista med indatamängds dokument som ska skapas senare. Anropa klientens `key_phrases()` funktion och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och de extraherade nyckel fraserna.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Utanför klient funktionen skapar du en ny funktion som heter `KeyPhraseExtractionExample()` som tar `TextAnalyticsClient`-objektet som skapades tidigare. Skapa en lista med `MultiLanguageInput` objekt som innehåller dokumenten som du vill analysera. Varje-objekt kommer att innehålla ett `id`, ett `language`och ett `text`-attribut. Attributet `text` lagrar texten som ska analyseras, `language` är språk texten och `id` kan vara vilket värde som helst. Anropa sedan klientens `ExtractKeyPhrases()` funktion.

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
