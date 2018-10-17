---
title: 'Snabbstart: SDK för nyhetssökning i Bing, Python'
titleSuffix: Azure Cognitive Services
description: Konfigurera konsolprogrammet för SDK för nyhetssökning i Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 8e4343b053835c0fc2219373ad60f96c7b80636a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803349"
---
# <a name="quickstart-bing-news-search-sdk-with-python"></a>Snabbstart: SDK för nyhetssökning i Bing med Python

SDK:t för nyhetssökning i Bing innehåller funktionerna i REST API:et för webbfrågor och parsning av resultat. 

[Källkoden till exemplen med SDK:t för nyhetssökning i Bing med Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) är tillgänglig på Git Hub.

## <a name="application-dependencies"></a>Programberoenden
Installera Python om du inte redan har det. Detta SDK är kompatibelt med Python 2.7, 3.3, 3.4, 3.5 och 3.6.

Den allmänna rekommendationen för Python-utveckling är att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html). Installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv). Du måste installera virtualenv för Python 2.7.
```
python -m venv mytestenv
```
Installera beroenden för SDK:t för nyhetssökning i Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Klient för nyhetssökning
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Lägg till importer:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Skapa en instans av `CognitiveServicesCredentials`. Instansiera klienten:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Sök efter resultat och skriv ut resultatet på den första webbsidan:
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")

```
Sök med filter efter de senaste nyheterna om ”artificiell intelligens” med parametrarna `freshness` och `sortBy`. Kontrollera antalet resultat och skriv ut `totalEstimatedMatches`, `name`, `url`, `description`, `published time` och `name of provider` för det första nyhetsresultatet.
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Sök efter kategorinyheter för film och TV-underhållning med säker sökning. Kontrollera antalet resultat och skriv ut `category`, `name`, `url`, `description`, `published time` och `name of provider` för det första nyhetsresultatet.
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Sök efter populära nyhetsämnen i Bing.  Kontrollera antalet resultat och skriv ut `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` och `image Url` för det första nyhetsresultatet.
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>Nästa steg

[Exempel med Cognitive Services SDK för Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


