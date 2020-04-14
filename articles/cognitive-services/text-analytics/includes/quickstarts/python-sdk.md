---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 04/8/2020
ms.author: aahi
ms.openlocfilehash: 2693fd6a84c221fdcbbe2d3511490805316a156b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274933"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

[v3 Referensdokumentation](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [v3 Biblioteks källkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 Paket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3-prover](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[v2 Referensdokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2 Biblioteks källkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2 Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2-prover](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* När du har din <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-prenumeration"  target="_blank">skapar Skapa en <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics-resurs en Text Analytics-resurs i Azure-portalen för att hämta din nyckel och slutpunkt. När den har distribuerats klickar du på **Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från den resurs du skapar för att ansluta ditt program till Text Analytics API. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den`F0`kostnadsfria prisnivån ( ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="install-the-client-library"></a>Installera klientbiblioteket

När du har installerat Python kan du installera klientbiblioteket med:

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

```console
pip install azure-ai-textanalytics==1.0.0b3
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), som innehåller kodexemplen i den här snabbstarten. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), som innehåller kodexemplen i den här snabbstarten. 

---

### <a name="create-a-new-python-application"></a>Skapa ett nytt pythonprogram

Skapa en ny Python-fil och skapa variabler för resursens Azure-slutpunkt och prenumerationsnyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektmodell

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Text Analytics-klienten `TextAnalyticsClient` är ett objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text som en batch. 

När batchbearbetning av text skickas till `documents`API:et som en lista över , som är `dictionary` objekt som innehåller en kombination `id`av , `text`och `language` attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprunget `language`och `id` kan vara valfritt värde. 

Svarsobjektet är en lista som innehåller analysinformationen för varje dokument. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Text Analytics-klienten är ett [TextAnalyticsClient-objekt](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) som autentiserar till Azure med hjälp av din nyckel. Klienten innehåller flera metoder för att analysera text, som en enda sträng eller en batch. 

Text skickas till API:et `documents`som `dictionary` en lista över `id` `text`, `language` som är objekt som innehåller en kombination av , och attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprunget `language`och `id` kan vara valfritt värde. 

---

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med Text Analytics-klientbiblioteket för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språkidentifiering](#language-detection)
* [Namngiven entitetsigenkänning](#named-entity-recognition-ner) 
* [Entitetslänkning](#entity-linking)
* [Extraktion av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en funktion för `TextAnalyticsClient` att instansiera objektet med din `key` och `endpoint` skapad ovan. Skapa sedan en ny klient. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Skapa en funktion för `TextAnalyticsClient` att instansiera objektet med din `key` och `endpoint` skapad ovan. Skapa sedan en ny klient. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Sentimentanalys

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en ny `sentiment_analysis_example()` funktion som kallas som tar `analyze_sentiment()` klienten som ett argument och anropar sedan funktionen. Det returnerade svarsobjektet innehåller sentimentetiketten och poängen för hela indatadokumentet, samt en sentimentanalys för varje mening.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
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

Autentisera ett klientobjekt och anropa funktionen [sentiment().](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) Iterera genom resultaten och skriv ut varje dokuments ID och sentimentpoäng. En poäng närmare 0 indikerar en negativ känsla, medan en poäng närmare 1 indikerar en positiv känsla.

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

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en ny `language_detection_example()` funktion som kallas som tar `detect_language()` klienten som ett argument och anropar sedan funktionen. Det returnerade svarsobjektet innehåller `primary_language` det identifierade `error` språket i om det lyckas och ett om inte.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indata. Du kan `country_hint` använda parametern för att ange en landskod med två bokstäver. Som standard api använder "USA" som standardlandInta, för att ta bort detta beteende kan `country_hint : ""`du återställa den här parametern genom att ställa in det här värdet till tom sträng . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
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

Med hjälp av klienten som skapats tidigare, ring [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och få resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID, och det första returnerade språket.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Resultat

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Namngiven entitetsigenkänning (NER)

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

> [!NOTE]
> I `3.0-preview`version : 
> * Entitetslänkning är en separat begäran än NER.

Skapa en ny `entity_recognition_example` funktion som kallas som tar `recognize_entities()` klienten som ett argument, sedan anropar funktionen och itererar genom resultaten. Det returnerade svarsobjektet innehåller listan `entity` över identifierade entiteter i om det lyckas och en `error` om inte. Skriv ut dess kategori och underkategori för varje identifierad entitet.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

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

## <a name="entity-linking"></a>Entity Linking

Skapa en ny `entity_linking_example()` funktion som kallas som tar `recognize_linked_entities()` klienten som ett argument, sedan anropar funktionen och itererar genom resultaten. Det returnerade svarsobjektet innehåller listan `entities` över identifierade entiteter i om det lyckas och en `error` om inte. Eftersom länkade entiteter identifieras unikt grupperas förekomster `entity` av samma `match` entitet under ett objekt som en lista över objekt.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Resultat

```console
Linked Entities:

    Name:  Altair 8800     Id:  Altair 8800     Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Confidence Score: 0.00     Length: 11

    Name:  Bill Gates     Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Confidence Score: 0.00     Length: 10

        Text: Gates
        Confidence Score: 0.00     Length: 5

    Name:  Paul Allen     Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Confidence Score: 0.00     Length: 10

    Name:  Microsoft     Id:  Microsoft     Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Confidence Score: 0.00     Length: 9

        Text: Microsoft
        Confidence Score: 0.00     Length: 9

    Name:  April 4     Id:  April 4     Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Confidence Score: 0.00     Length: 7

    Name:  BASIC     Id:  BASIC     Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Confidence Score: 0.00     Length: 5
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

> [!NOTE]
> I version 2.1 inkluderas entitetslänkning i NER-svaret.

Med hjälp av klienten som skapats tidigare anropar du [funktionen entiteter()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämtar resultatet. Sedan iterera genom resultaten och skriva ut varje dokuments ID och entiteterna som finns i det.

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


#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en ny `key_phrase_extraction_example()` funktion som kallas som tar `extract_key_phrases()` klienten som ett argument och anropar sedan funktionen. Resultatet kommer att innehålla en lista `key_phrases` över identifierade nyckelfraser i om det lyckas, och en `error` om inte. Skriv ut alla identifierade nyckelfraser.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

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

Med hjälp av klienten som skapats tidigare anropar du funktionen [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och får resultatet. Sedan iterera genom resultaten, och skriva ut varje dokuments ID, och de nyckelfraser som finns i den.

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