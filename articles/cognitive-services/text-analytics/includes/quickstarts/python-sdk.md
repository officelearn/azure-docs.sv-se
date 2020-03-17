---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 0803a847e9e864b361917df9f1a9c6b059ca2fe9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203445"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

[Referensdokumentation för v3](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b2/azure.ai.textanalytics.html) | [Bibliotekskällkod för v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Paket för v3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[Referensdokumentation för v2](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Bibliotekskällkod för v2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Paket för v2 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [Skapa en kostnadsfritt](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Skapa en resurs för Textanalys"  target="_blank">skapa en resurs för Textanalys <span class="docon docon-navigate-external x-hidden-focus"></span></a> i Microsoft Azure-portalen för att hämta din nyckel och slutpunkt. 
    * Du behöver nyckeln och slutpunkten från resursen som du skapade för att ansluta ditt program till API för textanalys. Du kommer att göra detta senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån för att testa tjänsten och uppgradera till en betald nivå för produktion senare.

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-client-library"></a>Installera klientbiblioteket

När du har installerat Python kan du installera klientbiblioteket med:

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), som innehåller kodexemplen i den här snabbstarten. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), som innehåller kodexemplen i den här snabbstarten. 

---

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa en ny Python-fil och skapa variabler för dina resursers Azure-slutpunkt och prenumerationsnyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektmodell

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Klienten för Textanalys är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en batch. 

Vid batchbearbetning skickas texten skickas till API:et som en lista med `documents`, som är `dictionary`-objekt som innehåller en kombination av attributen `id`, `text` och `language` beroende på vilken metod som används. `text`-attributet lagrar texten som ska analyseras i källa `language` och `id` kan vara vilket värde som helst. 

Svarsobjektet är en lista som innehåller analysinformationen om varje dokument. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Klienten för Textanalys är ett [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python)-objekt som autentiserar till Azure med din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch. 

Texten skickas till API:et som en lista med `documents`, som är `dictionary`-objekt som innehåller en kombination av attributen `id`, `text` och `language` beroende på vilken metod som används. `text`-attributet lagrar texten som ska analyseras i källa `language` och `id` kan vara vilket värde som helst. 

---

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med Textanalys-klientbiblioteket för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven enhet](#named-entity-recognition-ner) 
* [Entitetslänkning](#entity-linking)
* [Extrahering av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en funktion för att instansiera `TextAnalyticsClient`-objektet med `key` och `endpoint` som skapats ovan. Skapa sedan en ny klient. 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Skapa en funktion för att instansiera `TextAnalyticsClient`-objektet med `key` och `endpoint` som skapats ovan. Skapa sedan en ny klient. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Sentimentanalys

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en ny funktion med namnet `sentiment_analysis_example()` som tar klienten som ett argument och anropar sedan funktionen `analyze_sentiment()`. Det returnerade svarsobjektet innehåller sentiment-etiketten och resultatet för hela indatadokumentet, samt en attitydanalys för varje mening.


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Length: {}]".format(sentence.grapheme_length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))

            
sentiment_analysis_example(client)
```

### <a name="output"></a>Resultat

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00

[Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

[Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Autentisera ett klientobjekt och anropa funktionen [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-). Iterera genom resultat och skriv ut varje dokuments ID och sentimentpoäng. Ett resultat nära 0 visar negativt sentiment medan ett resultat nära 1 indikerar ett positivt sentiment.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Resultat

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Språkidentifiering

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en ny funktion med namnet `language_detection_example()` som tar klienten som ett argument och sedan anropar funktionen `detect_language()`. Det returnerade svarsobjektet innehåller det identifierade språket på `primary_language` om det lyckas och ett `error` om det misslyckas.

> [!Tip]
> I vissa fall kan det vara svårt att förtydliga språk baserat på indata. Du kan använda parametern `country_hint` för att ange en landskod på 2 bokstäver. Som standard använder API:et ”US” som standardinställd countryHint. Om du vill ta bort detta beteende kan du återställa parametern genom att ställa in värdet på tom sträng, `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        document = ["Ce document est rédigé en Français."]
        response = client.detect_language(inputs = document, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Resultat

```console
Language:  French
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Med hjälp av klienten som skapats tidigare anropar du [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämtar resultatet. Iterera sedan genom resultaten och skriv ut varje dokuments ID och det första returnerade språket.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Resultat

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngiven enhet (NER)

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

> [!NOTE]
> I version `3.0-preview`:
> * NER innehåller separata metoder för att identifiera personliga uppgifter. 
> * Entitetslänkning är en begäran som skiljer sig från NER.

Skapa en ny funktion med namnet `entity_recognition_example` som tar klienten som ett argument och sedan anropar funktionen `recognize_entities()` och itererar genom resultatet. Det returnerade svarsobjektet innehåller listan över identifierade entiteter `entity` om det lyckas och ett `error` om det misslyckas. Skriv ut kategori och underkategori (om det finns en sådan) för varje identifierad entitet.

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Resultat

```console
Named Entities:

    Text:    Seattle        Category:        Location       SubCategory:     GPE
    Length:          7      Confidence Score:        0.92

    Text:    last week      Category:        DateTime       SubCategory:     DateRange
    Length:          9      Confidence Score:        0.8
```

## <a name="using-ner-to-detect-personal-information"></a>Använd NER för att identifiera personliga uppgifter

Skapa en ny funktion med namnet `entity_pii_example()` som tar klienten som ett argument och sedan anropar funktionen `recognize_pii_entities()` och hämtar resultatet. Iterera sedan genom resultatet och skriv ut entiteterna.

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tLength: ", entity.grapheme_length, "\tScore: {0:.2f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>Resultat

```console
Personally Identifiable Information Entities:
    Text:  123-12-1234      Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Length:  11     Score: 0.85
```


## <a name="entity-linking"></a>Entity Linking

Skapa en ny funktion med namnet `entity_linking_example()` som tar klienten som ett argument och sedan anropar funktionen `recognize_linked_entities()` och itererar genom resultatet. Det returnerade svarsobjektet innehåller listan över identifierade entiteter `entities` om det lyckas och ett `error` om det misslyckas. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `entity`-objekt som en lista över `match`-objekt.

```python
def entity_linking_example(client):

    try:
        document = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(inputs= document)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.2f}".format(match.score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Resultat

```console
Linked Entities:

    Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.78     Length: 11

    Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.55     Length: 10

        Text: Gates
        Score: 0.55     Length: 5

    Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.53     Length: 10

    Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.47     Length: 9

        Text: Microsoft
        Score: 0.47     Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.25     Length: 7

    Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.28     Length: 5

```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

> [!NOTE]
> I version 2.1 ingår entitetslänkning i NER-svaret.

Med hjälp av klienten som skapats tidigare anropar du funktionen [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämtar resultatet. Iterera sedan genom resultaten och skriv ut varje dokuments ID och entiteterna som finns i det.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

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

---

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser


#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en ny funktion med namnet `key_phrase_extraction_example()` som tar klienten som ett argument och sedan anropar funktionen `extract_key_phrases()`. Resultatet innehåller listan över identifierade entiteter `key_phrases` om det lyckas och ett `error` om det misslyckas. Skriv ut identifierade nyckelfraser.

```python
def key_phrase_extraction_example(client):

    try:
        document = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(inputs= document)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Resultat

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Med hjälp av klienten som skapats tidigare anropar du funktionen [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämtar resultatet. Iterera sedan genom resultaten och skriv ut varje dokuments ID och nyckelfraserna som finns i det.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


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

---