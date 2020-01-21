---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 518e6d544547b808b278121bf6364dcd1590bd6f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281206"
---
<a name="HOLTop"></a>

[Referens dokumentation](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [Library Source Code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Package (PiPy) | -](https://pypi.org/project/azure-ai-textanalytics/) [exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * I den här snabb starten används version `3.0-preview` av Textanalys-klient biblioteket, som innehåller en offentlig för hands version för bättre [Attitydanalys](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [ner (Named Entity Recognition)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Du kan till exempel importera klienten från `azure.ai.textanalytics.aio`-namnrymden och anropa `analyze_sentiment()`, i stället för att `analyze_sentiment()` från namn området `azure.ai.textanalytics`.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-text-analytics-azure-resource"></a>Skapa en Textanalys Azure-resurs

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa en ny python-fil och skapa variabler för resursens Azure-slut punkt och prenumerations nyckel.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text som en batch. I den här snabb starten används en samling funktioner för att snabbt skicka enstaka dokument.

När batchbearbetning av text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination av `id`, `text`och `language` attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprungs `language`och `id` kan vara vilket värde som helst. När du bearbetar enskilda dokument behövs bara en `text` indata som visas i exemplen nedan.  

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med Textanalys klient biblioteket för python:

* [Attitydanalys](#sentiment-analysis) (offentlig för hands version)
* [Språk identifiering](#language-detection)
* [Identifiering av namngiven entitet](#named-entity-recognition-public-preview) (offentlig för hands version)
* [Igenkänning av namngivna enheter – personlig information](#named-entity-recognition---personal-information-public-preview) (offentlig för hands version)
* [Länkning av entitet](#entity-linking)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Känsloanalys

> [!NOTE]
> Koden nedan är för sentiment Analysis v3, som finns i offentlig för hands version.

Skapa en ny funktion som kallas `sentiment_analysis_example()` som tar slut punkten och nyckeln som argument, och anropar sedan funktionen `single_analyze_sentiment()`. Det returnerade Response-objektet innehåller sentiment-etiketten och poängen i hela indatamängden, samt en sentiment-analys för varje mening.


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, key=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>Resultat

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>Språkspårning

Skapa en ny funktion som kallas `language_detection_example()` som tar slut punkten och nyckeln som argument, och anropar sedan funktionen `single_detect_languages()`. Det returnerade Response-objektet kommer att innehålla det identifierade språket i `detected_languages` om det lyckas, och en `error` om inte.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda parametern `country_hint` för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange det här värdet till tom sträng `country_hint : ""`. 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, key=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>Resultat

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>Identifiering av namngiven entitet (offentlig för hands version)

> [!NOTE]
> Koden nedan är för namngiven enhets igenkänning v3, som finns i offentlig för hands version.

Skapa en ny funktion som kallas `entity_recognition_example` som tar slut punkten och nyckeln som argument, och anropar sedan funktionen `single_recognize_entities()` och itererar igenom resultaten. Det returnerade Response-objektet innehåller listan över identifierade entiteter i `entity` om det lyckades och en `error` om inte. För varje identifierad entitet skriver du ut dess typ och under typ om den finns.

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>Resultat

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Igenkänning av namngivna enheter – personlig information (offentlig för hands version)

> [!NOTE]
> Koden nedan är för att identifiera personlig information med hjälp av namngivna entitets igenkänning v3, som finns i offentlig för hands version.

Skapa en ny funktion som kallas `entity_pii_example()` som tar slut punkten och nyckeln som argument, och anropar sedan funktionen `single_recognize_pii_entities()` och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut entiteterna.

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>Resultat

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>Entitetslänkning

Skapa en ny funktion som kallas `entity_linking_example()` som tar slut punkten och nyckeln som argument, och anropar sedan funktionen `single_recognize_linked_entities()` och itererar igenom resultaten. Det returnerade Response-objektet innehåller listan över identifierade entiteter i `entities` om det lyckades och en `error` om inte. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `entity` objekt som en lista över `match` objekt.

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, key=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>Resultat

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>Extraktion av nyckelfraser

Skapa en ny funktion som kallas `key_phrase_extraction_example()` som tar slut punkten och nyckeln som argument, och anropar sedan funktionen `single_extract_key_phrases()`. Resultatet kommer att innehålla en lista över identifierade nyckel fraser i `key_phrases` om det lyckas, och en `error` om inte. Skriv ut alla identifierade nyckel fraser.

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, key=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>Resultat

```console
    Key Phrases:
         cat
         veterinarian
```