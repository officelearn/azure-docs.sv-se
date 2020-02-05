---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 30c65abcbf469dd4f6e8987aa1e2ee8a36fef17a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987938"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

[v3 referens dokumentation](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [v3 biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3-paket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

[v2 referens dokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2 bibliotek käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2-paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

---

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa en ny python-fil och skapa variabler för resursens Azure-slut punkt och prenumerations nyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objekt modell

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Textanalys-klienten är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text som en batch. I den här snabb starten används en samling funktioner för att snabbt skicka enstaka dokument.

När batchbearbetning av text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination av `id`, `text`och `language` attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprungs `language`och `id` kan vara vilket värde som helst. När du bearbetar enskilda dokument behövs bara en `text` indata som visas i exemplen nedan.  

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Textanalys-klienten är ett [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) -objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch. 

Text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination av `id`, `text`och `language` attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprungs `language`och `id` kan vara vilket värde som helst. 

---

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du utför följande uppgifter med Textanalys klient biblioteket för python:

* [Attitydanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner) 
* [Länkning av entitet](#entity-linking)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Känsloanalys

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Autentisera ett klient objekt och anropa funktionen [sentiment ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) . Upprepa resultaten och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Resultat

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Språkspårning

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Med hjälp av klienten som skapades tidigare anropar [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och får resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och det första returnerade språket.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Resultat

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

> [!NOTE]
> I version `3.0-preview`:
> * NER innehåller separata metoder för att identifiera personlig information. 
> * Enhets länkning är en separat begäran än NER.

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

## <a name="using-ner-to-detect-personal-information"></a>Använda NER för att identifiera personlig information

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

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

> [!NOTE]
> I version 2,1 ingår enhets länkning i NER-svaret.

Med hjälp av klienten som skapades tidigare anropar du funktionen [entiteter ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och entiteterna i det.

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

## <a name="key-phrase-extraction"></a>Extraktion av nyckelfraser


#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Med hjälp av den klient som skapades tidigare anropar du funktionen [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och de viktigaste fraserna i det.

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