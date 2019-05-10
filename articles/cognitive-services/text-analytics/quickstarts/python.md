---
title: 'Snabbstart: Använda Python för att anropa API för textanalys'
titleSuffix: Azure Cognitive Services
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med API för textanalys i Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/09/2019
ms.author: aahi
ms.openlocfilehash: 9d6dfb79d02df3eebe33e67743ceaf97fc0a2a77
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519357"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Snabbstart: Med hjälp av Python REST-API för att anropa tjänsten Text Analytics Cognitive 
<a name="HOLTop"></a>

Använd den här snabbstarten om du vill analysera språk med REST API för textanalys och Python. Den här artikeln visar hur du [identifiera språk](#Detect), [analysera sentiment](#SentimentAnalysis), [extrahera nyckelfraser](#KeyPhraseExtraction), och [identifiera länkade entiteter](#Entities).

Du kan köra det här exemplet från kommandoraden eller som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder ge en skylt:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Se [API-definitionerna](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API:erna.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 3.x](https://python.org)

* Den [slutpunkt och åtkomstnyckel](../How-tos/text-analytics-how-to-access-key.md) som genererades för dig under registreringen.

* Bibliotek för Python-begäranden
    
    Du kan installera biblioteket med det här kommandot:

    ```console
    pip install --upgrade requests
    ```

* Om du använder anteckningsboken Binder lokalt kan du behöva uppdatera [IPython](https://ipython.org/install.html):
    
    ```console
    pip install --upgrade IPython
    ```

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt Python-program i din favoritredigerare eller IDE. Lägg till följande importer i filen.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
from IPython.display import HTML
```

Skapa variabler för din prenumerationsnyckel och slutpunkten för den REST API för textanalys. Kontrollera att regionen i slutpunkten som motsvarar den som du använde när du registrerade dig (till exempel `westcentralus`). Om du använder en kostnadsfri utvärderingsversion nyckel, behöver du inte ändra något.
    
```python
subscription_key = "<ADD YOUR KEY HERE>"
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
```

I följande avsnitt beskrivs hur du anropar var och en av de API-funktioner.

<a name="Detect"></a>

## <a name="detect-languages"></a>Identifiera språk

Lägg till `languages` till textanalys basslutpunktens för att bilda språk identifiering av URL: en. Exempel: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
    
```python
language_api_url = text_analytics_base_url + "languages"
```

Nyttolasten i API: n består av en lista över `documents`, vilket är tupplar som innehåller en `id` och en `text` attribut. Den `text` attributet lagrar texten som ska analyseras, och `id` kan vara vilket värde. 

```python
documents = { "documents": [
    { "id": "1", "text": "This is a document written in English." },
    { "id": "2", "text": "Este es un document escrito en Español." },
    { "id": "3", "text": "这是一个用中文写的文件" }
]}
```

Använd begäranden-biblioteket för att skicka dokument till API: et. Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik och skicka begäran med `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Utdata

```json
{
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analysera sentiment

Om du vill identifiera sentimentet (som sträcker sig mellan positiva eller negativa) med en uppsättning dokument, lägger du till `sentiment` till textanalys basslutpunktens för att bilda språk identifiering av URL: en. Exempel: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = text_analytics_base_url + "sentiment"
```

Som med språket identifiering exempel skapa en ordlista med en `documents` nyckel som består av en lista över dokument. Varje dokument är en tuppel som består av `id`, `text` som ska analyseras och textens `language`. 

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Använd begäranden-biblioteket för att skicka dokument till API: et. Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik och skicka begäran med `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Utdata

Sentimentresultatet för ett dokument är mellan 0,0 och 1,0, med en högre poäng som anger en mer positiv attityd.

```json
{
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[

  ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrahera nyckelfraser
 
Extrahera nyckelfraser från en uppsättning dokument genom att lägga till `keyPhrases` till textanalys basslutpunktens för att bilda språk identifiering av URL: en. Exempel: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = text_analytics_base_url + "keyPhrases"
```

Den här samlingen av dokument är densamma som används för sentiment analysis-exemplet.

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Använd begäranden-biblioteket för att skicka dokument till API: et. Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik och skicka begäran med `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Utdata

```json
{
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[

  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identifiera entiteter

Lägg till för att identifiera välkända entiteter (personer, platser och saker) i textdokument `keyPhrases` till textanalys basslutpunktens för att bilda språk identifiering av URL: en. Exempel: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
entities_url = text_analytics_base_url + "keyPhrases"
```

Skapa en samling dokument, som i föregående exempel. 

```python
documents = {"documents" : [
  {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

Använd begäranden-biblioteket för att skicka dokument till API: et. Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik och skicka begäran med `requests.post()`.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
```

### <a name="output"></a>Utdata

```json
{
  "documents":[
    {
      "id":"1",
      "keyPhrases":[
        "Bill Gates",
        "Paul Allen",
        "BASIC interpreters",
        "Altair",
        "Microsoft"
      ]
    }
  ],
  "errors":[]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
