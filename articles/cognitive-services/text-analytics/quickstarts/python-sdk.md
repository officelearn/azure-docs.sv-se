---
title: 'Snabbstart: Anropa tjänsten Textanalys med python SDK'
titleSuffix: Azure Cognitive Services
description: Få information och kod exempel som hjälper dig att snabbt komma igång med API för textanalys i Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: c24979d9aef74b6cc840427a010b9ce70f2c0b8a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356947"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Snabbstart: Anropa tjänsten Textanalys med python SDK 
<a name="HOLTop"></a>

Använd den här snabb starten för att börja analysera språk med Textanalys SDK för python. Även om Textanalys REST API är kompatibel med de flesta programmeringsspråk, ger SDK ett enkelt sätt att integrera tjänsten i dina program utan att serialisera och deserialisera JSON. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.x](https://www.python.org/)

* Textanalys [SDK för python](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) du kan installera paketet med:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Du måste också ha [slut punkten och åtkomst nyckeln](../How-tos/text-analytics-how-to-access-key.md) som genererades åt dig under registreringen.

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt python-program i din favorit redigerare eller IDE. Lägg sedan till följande import uttryck i filen.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Autentisera dina autentiseringsuppgifter

> [!Tip]
> Vi rekommenderar att du använder [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)för säker distribution av hemligheter i produktions system.
>

När du har gjort en variabel för din textanalys prenumerations nyckel `CognitiveServicesCredentials` instansierar du ett objekt med det.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Skapa en Textanalys-klient

Skapa ett nytt `TextAnalyticsClient` objekt med `credentials` och `text_analytics_url` som en parameter. Använd rätt Azure-region för din Textanalys-prenumeration (till `westcentralus`exempel).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Nytto lasten till API: et består av en `documents`lista över, som är ord `id` listor som `text` innehåller ett och ett-attribut. Attributet lagrar texten som ska analyseras `id` och kan vara vilket värde som helst. `text` 

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    },
    {
        "id": "2",
        "language": "en",
        "text": "This was a waste of my time. The speaker put me to sleep."
    },
    {
        "id": "3",
        "language": "es",
        "text": "No tengo dinero ni nada que dar..."
    },
    {
        "id": "4",
        "language": "it",
        "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    }
]
```

`sentiment()` Anropa funktionen och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Språkidentifiering

Skapa en lista över ord listor som innehåller det dokument som du vill analysera. Attributet lagrar texten som ska analyseras `id` och kan vara vilket värde som helst. `text` 

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    },
    {
        'id': '2',
        'text': 'Este es un document escrito en Español.'
    },
    {
        'id': '3',
        'text': '这是一个用中文写的文件'
    }
]
```

Anropa `detect_language()` och hämta resultatet med hjälp av klienten som skapades tidigare. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och det första returnerade språket.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Enhets igenkänning

Skapa en lista över ord listor som innehåller dokumenten som du vill analysera. Attributet lagrar texten som ska analyseras `id` och kan vara vilket värde som helst. `text` 


```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es",
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

Genom att använda klienten som skapades tidigare `entities()` anropar du funktionen och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och entiteterna i det.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Output

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en lista över ord listor som innehåller dokumenten som du vill analysera. Attributet lagrar texten som ska analyseras `id` och kan vara vilket värde som helst. `text` 


```python
documents = [
    {
        "id": "1",
        "language": "ja",
        "text": "猫は幸せ"
    },
    {
        "id": "2",
        "language": "de",
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4",
        "language": "es",
        "text": "A mi me encanta el fútbol!"
    }
]
```

Genom att använda klienten som skapades tidigare `key_phrases()` anropar du funktionen och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och de viktigaste fraserna i det.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```

### <a name="output"></a>Output

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också

* [Vad är API för textanalys?](../overview.md)
* [Exempel på användar scenarier](../text-analytics-user-scenarios.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
