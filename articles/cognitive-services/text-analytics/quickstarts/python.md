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
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 69eb3789586233b824da1ef6a9c338b07281f324
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828113"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Snabbstart: Anropa den kognitiva tjänsten för textanalys med hjälp av Python 
<a name="HOLTop"></a>

Den här genomgången visar hur du [identifierar språk](#Detect), [analyserar sentiment](#SentimentAnalysis) och [extraherar nyckelfraser](#KeyPhraseExtraction) med hjälp av [API:er för textanalys](//go.microsoft.com/fwlink/?LinkID=759711) med Python.

Du kan köra det här exemplet från kommandoraden eller som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder ge en skylt:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

### <a name="command-line"></a>Kommandorad

Du kan behöva uppdatera [IPython](https://ipython.org/install.html), kärnan i Jupyter:
```bash
pip install --upgrade IPython
```

Du kan behöva uppdatera den [begäranden](http://docs.python-requests.org/en/master/) bibliotek:
```bash
pip install requests
```

Se [API-definitionerna](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API:erna.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

* Den [slutpunkt och åtkomstnyckel](../How-tos/text-analytics-how-to-access-key.md) som genererades för dig under registreringen.

* Följande importer, prenumerationsnyckel, och `text_analytics_base_url` används för alla snabbstarter nedan. Lägg till importer.

    ```python
    import requests
    # pprint is pretty print (formats the JSON)
    from pprint import pprint
    from IPython.display import HTML
    ```
    
    Lägg till följande rader och sedan ersätta `subscription_key` med en giltig prenumeration-nyckel som du fick tidigare.
    
    ```python
    subscription_key = '<ADD KEY HERE>'
    assert subscription_key
    ```
    
    Därefter lägger du till den här raden och sedan kontrollera att regionen i `text_analytics_base_url` motsvarar den som du använde när du konfigurerar tjänsten. Om du använder en kostnadsfri utvärderingsversion nyckel, behöver du inte ändra något.
    
    ```python
    text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
    ```

<a name="Detect"></a>

## <a name="detect-languages"></a>Identifiera språk

API:et för språkidentifiering identifierar språket i ett textdokument, med metoden [Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) (Identifiera språk). Tjänstslutpunkt för språkidentifierings-API för din region finns via följande webbadress:

```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages


Nyttolasten i API: n består av en lista över `documents`, och var och en innehåller i sin tur ett `id`- och ett `text`-attribut. `text`-attributet lagrar texten som ska analyseras. 

Ersätt ordlistan `documents` med annan text för språkidentifiering.

```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Nästa kodrader anropar språkidentifiering-API med hjälp av `requests`-biblioteket i Python för att identifiera språket i dokumenten.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

Följande rader med kod renderar JSON-data som en HTML-tabell.

```python
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

Lyckad JSON-svar:

```json
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
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analysera sentiment

API för Attitydstextanalys Analysis identifierar sentiment (intervall mellan positiva eller negativa) av en uppsättning textposter, med hjälp av den [Sentiment metoden](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). I följande exempel poängsätts två dokument, ett på engelska och ett annat på spanska.

Tjänstslutpunkten för attitydanalys är tillgänglig för din region via följande webbadress:

```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment

Precis som i exemplet på språkidentifiering tillhandahålls tjänsten med en ordlista med en `documents`-nyckel som består av en lista över dokument. Varje dokument är en tuppel som består av `id`, `text` som ska analyseras och textens `language`. Du kan använda språkidentifierings-API:et i föregående avsnitt för att fylla i det här fältet.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Sentiment-API:et kan nu användas för att analysera dokument för deras sentiment.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

Lyckad JSON-svar:

```json
{'documents': [{'id': '1', 'score': 0.7673527002334595},
                {'id': '2', 'score': 0.18574094772338867},
                {'id': '3', 'score': 0.5}],
    'errors': []}
```

Sentimentresultatet för ett dokument är mellan 0,0 och 1,0, med en högre poäng som anger en mer positiv attityd.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrahera nyckelfraser

API:et för extrahering av diskussionsämnen extraherar diskussionsämnen från ett textdokument, med metoden [Key Phrases](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) (Diskussionsämnen). I följande avsnitt i genomgången extraheras nyckelfraser för både engelska och spanska dokument.

Tjänstslutpunkten för tjänsten för nyckelfrasextrahering nås via följande webbadress:

```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases

Samlingen av dokument är samma som den du använde för attitydanalysen.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

JSON-objekt kan återges som en HTML-tabell med följande rader med kod:

```python
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

Nästa kodrader anropar språkidentifiering-API med hjälp av `requests`-biblioteket i Python för att identifiera språket i dokumenten.
```python
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

Lyckad JSON-svar:
```json
{'documents': [
    {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
    {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
    {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
    {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
    'errors': []
}
```

## <a name="identify-entities"></a>Identifiera entiteter

API:et för entiteter identifierar välkända entiteter i ett textdokument med hjälp av [metoden Entiteter](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). I följande exempel identifieras entiteter för engelska dokument.

Tjänstslutpunkten för entitetslänkningens tjänst nås via följande webbadress:

```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities

Samlingen med dokument finns nedan:

```python
documents = {'documents' : [
  {'id': '1', 'text': 'Microsoft is an It company.'}
]}
```
Dokumenten kan nu skickas till API för textanalys för att ta emot svaret.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

Lyckad JSON-svar:
```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
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
