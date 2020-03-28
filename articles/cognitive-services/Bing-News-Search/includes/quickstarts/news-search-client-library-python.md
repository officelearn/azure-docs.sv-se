---
title: Snabbstart för Bing News Search Python-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503883"
---
Använd den här snabbstarten för att börja söka efter nyheter med klientbiblioteket Bing News Search för Python. Bing News Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Krav

* [Python (svenska)](https://www.python.org/) 2.x eller 3.x

Vi rekommenderar att du använder en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html) för din Python-utveckling. Du kan installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv). Du måste installera en virtualenv för Python 2.7. Du kan skapa en virtuell miljö med:

```console
python -m venv mytestenv
```

Du kan installera klientbiblioteksberoendena för Bing News Search med det här kommandot:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och importera följande bibliotek. Skapa en variabel för din prenumerationsnyckel eller sökterm.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Initiera klienten och skicka en begäran

1. Skapa en instans av `CognitiveServicesCredentials`.
    
    ```python
    client = NewsSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
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
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
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
> [Skapa en ensidig webbapp](../../tutorial-bing-news-search-single-page-app.md)
