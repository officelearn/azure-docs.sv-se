---
title: Nyheter Sök SDK Python quickstart | Microsoft Docs
description: Installationsprogrammet för Nyheter Sök SDK-konsolprogram.
titleSuffix: Azure News Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6d212d1477ecf583a038e33e72aab3d60f6aa050
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355272"
---
# <a name="news-search-sdk-python-quickstart"></a>Nyheter Sök SDK Python Snabbstart

Nyheter Sök SDK innehåller funktionerna i REST API för webb-frågor och tolkning resultat. 

Den [källkoden för Python Bing News Sök SDK-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) är tillgänglig på Git-hubben.

## <a name="application-dependencies"></a>Programberoenden
Om du inte redan har det installera Python. SDK är kompatibelt med Python 2.7, 3.3, 3.4, 3.5 och 3,6.

Allmänna rekommendationer för utveckling av Python är att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html). Installera och starta den virtuella miljön med den [venv modulen](https://pypi.python.org/pypi/virtualenv). Du måste installera virtuell miljö för Python 2.7.
```
python -m venv mytestenv
```
Installera Bing News Sök SDK beroenden:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Nyheter Sök klienten
Hämta en [kognitiva åtkomstnyckeln](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Lägg till importen:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Skapa en instans av `CognitiveServicesCredentials`. Skapa en instans av klienten:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Sök efter resultat och skriva ut det första resultatet webbsida:
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
Söka med filter för de senaste nyheterna ”artificiell Intelligence” med `freshness` och `sortBy` parametrar. Kontrollera antalet resultat och skriva ut `totalEstimatedMatches`, `name`, `url`, `description`, `published time`, och `name of provider` för det första nyheter objekt resultatet.
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
Sök kategori Nyheter för filmer och TV underhållning med säker sökning. Kontrollera antalet resultat och skriva ut `category`, `name`, `url`, `description`, `published time`, och `name of provider` för det första nyheter objekt resultatet.
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
Sök trender Nyheter i Bing.  Kontrollera antalet resultat och skriva ut `name`, `text of query`, `webSearchUrl`, `newsSearchUrl`, och `image Url` för det första resultatet nyheter.
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

[Kognitiva Services Python SDK-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


