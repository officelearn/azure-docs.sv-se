---
title: 'Snabbstart: Använda Bing-webbsökning SDK för Python'
description: Installationsprogrammet för Web Search SDK-konsolprogram.
titleSuffix: Azure Cognitive Services Web search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: faf43d84724cdbf799219c120f87dfc333c5026f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888534"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Snabbstart: Använda Bing-webbsökning SDK för Python

Bing Web Search SDK innehåller funktionen för REST-API för webbfrågor och parsa resultat.

Den [källkod för Python Bing Web Search SDK-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/web_search_samples.py) finns på GitHub.

## <a name="application-dependencies"></a>Programberoenden
Om du inte redan har det kan du installera Python. SDK: N är kompatibel med Python 2.7, 3.3, 3.4, 3.5 och 3.6.

Allmän rekommendation för Python-utveckling är att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html).
Installera och initiera den virtuella miljön med den [venv modulen](https://pypi.python.org/pypi/virtualenv). Du måste installera virtuell miljö för Python 2.7.
```
python -m venv mytestenv
```
Installera SDK för Bing Web Search beroenden:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```
## <a name="web-search-client"></a>Sök webbklienten
Hämta en [Cognitive Services prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Search*.
Lägg till import och skapa en instans av den `CognitiveServicesCredentials`:
```
from azure.cognitiveservices.search.websearch import WebSearchAPI
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Sedan kan skapa en instans av klienten:
```
client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Sök efter resultat och skriva ut det första resultatet webbsida:
```
web_data = client.web.search(query="Yosemite")
print("\r\nSearched for Query# \" Yosemite \"")

# WebPages
if web_data.web_pages.value:

    print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

    first_web_page = web_data.web_pages.value[0]
    print("First web page name: {} ".format(first_web_page.name))
    print("First web page URL: {} ".format(first_web_page.url))

else:
    print("Didn't see any Web data..")
```
Skriva ut andra resultattyper, inklusive bilder, nyheter och videor:
```
# Images
if web_data.images.value:

    print("\r\nImage Results#{}".format(len(web_data.images.value)))

    first_image = web_data.images.value[0]
    print("First Image name: {} ".format(first_image.name))
    print("First Image URL: {} ".format(first_image.url))

else:
    print("Didn't see any Image..")

# News
if web_data.news.value:

    print("\r\nNews Results#{}".format(len(web_data.news.value)))

    first_news = web_data.news.value[0]
    print("First News name: {} ".format(first_news.name))
    print("First News URL: {} ".format(first_news.url))

else:
    print("Didn't see any News..")

# Videos
if web_data.videos.value:

    print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

    first_video = web_data.videos.value[0]
    print("First Videos name: {} ".format(first_video.name))
    print("First Videos URL: {} ".format(first_video.url))

else:
    print("Didn't see any Videos..")

```
Sök efter (bästa restauranger i Seattle) kontrollerar antal resultat och skriva ut `name` och `URL` första resultatet.
```
def web_results_with_count_and_offset(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
        print("\r\nSearched for Query# \" Best restaurants in Seattle \"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))```

```
Sök efter ”xbox” med `response_filter` tilldelats `News`.  Skriva ut information om Nyhetsresultat.
```
def web_search_with_response_filter(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="xbox", response_filter=["News"])
        print("\r\nSearched for Query# \" xbox \" with response filters \"News\"")

        # News attribute since I filtered "News"
        if web_data.news.value:

            print("Webpage Results#{}".format(len(web_data.news.value)))

            first_web_page = web_data.news.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Sök med frågan ”Niagara hamnar”, med hjälp av `answerCount` och `promote` parametrar. Skriva ut information om resultat.
```
def web_search_with_answer_count_promote_and_safe_search(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(
            query="Niagara Falls",
            answer_count=2,
            promote=["videos"],
            safe_search=SafeSearch.strict  # or directly "Strict"
        )
        print("\r\nSearched for Query# \" Niagara Falls\"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Nästa steg

[Cognitive Services SDK för Python-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
