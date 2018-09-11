---
title: 'Snabbstart: Använda Python för att anropa API för textanalys | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med API för textanalys i Microsoft Cognitive Services på Azure.
services: cognitive-services
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 8e570aac2c2d89a8147d179c4b0f9155497c5188
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298700"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Snabbstart: Använda Python för att anropa tjänsten Text Analytics Cognitive
<a name="HOLTop"></a>

Den här genomgången visar hur du [identifiera språk](#Detect), [analysera sentiment](#SentimentAnalysis), och [extrahera nyckelfraser](#KeyPhraseExtraction) med hjälp av den [Text Analytics-API: er](//go.microsoft.com/fwlink/?LinkID=759711)med Python.

Du kan köra det här exemplet som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder ge en skylt: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Referera till den [API-definitioner](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API: erna.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API för textanalys**. Du kan använda den **kostnadsfri nivå för 5 000 transaktioner per månad** att slutföra den här genomgången.

Du måste också ha den [slutpunkt och åtkomstnyckel](../How-tos/text-analytics-how-to-access-key.md) som genererades för dig under registreringen. 

Om du vill fortsätta med den här genomgången, Ersätt `subscription_key` med en giltig prenumeration-nyckel som du fick tidigare.


```python
subscription_key = None
assert subscription_key
```

Därefter kontrollerar du att regionen i `text_analytics_base_url` motsvarar den som du använde när du konfigurerar tjänsten. Om du använder en kostnadsfri utvärderingsversion nyckel, behöver du inte ändra något.


```python
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
```

<a name="Detect"></a>

## <a name="detect-languages"></a>Identifiera språk

Language identifiering API identifierar språket för dokument, med hjälp av den [identifiera språk metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). Tjänsteslutpunkt för språkidentifiering API för din region finns via följande URL:


```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages


Nyttolasten i API: n består av en lista över `documents`, och var av som i sin tur innehåller en `id` och en `text` attribut. Den `text` attributet lagrar texten som ska analyseras. 

Ersätt den `documents` ordlista med annan text för språkidentifiering. 


```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Nästa få kodrader kalla in i språk identifiering API med hjälp av den `requests` biblioteket i Python för att identifiera språket i dokumenten.


```python
import requests
from pprint import pprint
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}


Följande rader med kod renderas JSON-data som en HTML-tabell.


```python
from IPython.display import HTML
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analysera sentiment

API för Attitydstextanalys Analysis detexts känsla av en uppsättning textposter, med hjälp av den [Sentiment metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). I följande exempel poängsätter två dokument, en i engelska och en annan på spanska.

Tjänstens slutpunkt för sentimentanalys är tillgänglig för din region via följande URL:


```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment


Som med exempel språk identifiering av tjänsten tillhandahålls en ordlista med en `documents` nyckel som består av en lista över dokument. Varje dokument är en tuppel som består av den `id`, `text` analyseras och `language` av texten. Du kan använda språkidentifiering API i föregående avsnitt för att fylla i det här fältet. 


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Sentiment API kan nu användas för att analysera dokument för deras sentiment.


```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

    {'documents': [{'id': '1', 'score': 0.7673527002334595},
                   {'id': '2', 'score': 0.18574094772338867},
                   {'id': '3', 'score': 0.5}],
     'errors': []}


Sentimentresultatet för ett dokument är mellan $ $0 och 1 USD$, med en högre poäng som anger en mer positiv attityd.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrahera nyckelfraser

Key frasen extrahering API: et extraherar nyckelfraser från en text dokument, med hjälp av den [Nyckelfraser metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Det här avsnittet av den här genomgången extraherar viktiga fraser för både engelska och spanska dokument.

Tjänstslutpunkt för tjänsten nyckel diskussionsämne nås via följande URL:


```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases


Samling av dokument är samma som det du använde för attitydanalys.


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```


    {'documents': [
        {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
        {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
        {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
        {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
     'errors': []
    }


JSON-objekt kan återigen återges som en HTML-tabell med följande rader med kod:


```python
from IPython.display import HTML
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

## <a name="identify-linked-entities"></a>Identifiera länkade entiteter

API för Entity Linking identifierar välkända entiteter i en text dokument, med hjälp av den [Entitetslänkning metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). I följande exempel identifierar entiteter för dokument på engelska.

Tjänstslutpunkt för tjänsten entitet länkande nås via följande URL:


```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/entities


Samling av dokument är nedan:


```python
documents = {'documents' : [
  {'id': '1', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.'},
  {'id': '2', 'text': 'The Seattle Seahawks won the Super Bowl in 2014.'}
]}
```

Dokumenten kan nu skickas till API för textanalys för att ta emot svaret.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```
    {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "name": "Xbox One",
                        "matches": [
                            {
                                "text": "XBox One",
                                "offset": 23,
                                "length": 8
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Xbox One",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                        "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                    },
                    {
                        "name": "Ultra-high-definition television",
                        "matches": [
                            {
                                "text": "4K",
                                "offset": 63,
                                "length": 2
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Ultra-high-definition television",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                        "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                    }
                ]
            },
            {
                "id": "2",
                "entities": [
                    {
                        "name": "2013 Seattle Seahawks season",
                        "matches": [
                            {
                                "text": "Seattle Seahawks",
                                "offset": 4,
                                "length": 16
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "2013 Seattle Seahawks season",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                        "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                    }
                ]
            }
        ],
        "errors": []
    }

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Powerbi](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också 

 [Översikt över text Analytics](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
