---
title: 'Snabbstart: Utföra en nyhetssökning – SDK för nyhetssökning i Bing för Python'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att söka efter nyheter med SDK för nyhetssökning i Bing för Python och bearbeta svaret.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 7927e680194d682ab9ee48320afa42ba29c676dc
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259503"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Snabbstart: Utför en nyhetssökning med SDK för nyhetssökning i Bing för Python

Använd den här snabbstarten om du vill börja söka efter nyheter med SDK för nyhetssökning i Bing för Python. Även om Nyhetssökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python](https://www.python.org/) 2.x eller 3.x

Vi rekommenderar att du använder en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html) för din Python-utveckling. Du kan installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv). Du måste installera en virtualenv för Python 2.7. Du kan skapa en virtuell miljö med:

```console
python -m venv mytestenv
```

Du kan installera beroendena för SDK för Nyhetssökning i Bing med det här kommandot:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och importera följande bibliotek. Skapa en variabel för din prenumerationsnyckel eller sökterm.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Initiera klienten och skicka en begäran

1. Skapa en instans av `CognitiveServicesCredentials`. Instantiera klienten:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Skicka en sökfråga till API:et för nyhetssökning i Bing och lagra svaren.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Parsa svaret

Om några sökresultat hittas skriver du ut resultatet på den första webbsidan:

```python
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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
[Skapa en enkelsidig webbapp](tutorial-bing-news-search-single-page-app.md)