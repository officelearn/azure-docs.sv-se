---
title: 'Snabbstart: SDK för videosökning, Python'
titleSuffix: Azure Cognitive Services
description: Konfiguration av konsolprogrammet för SDK för videosökning.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/15/2018
ms.author: rosh
ms.openlocfilehash: 2c099f3520b697e8ec2f7ef39c4525298182a697
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307924"
---
# <a name="quickstart-bing-video-search-sdk-with-python"></a>Snabbstart: SDK för videosökning i Bing med Python

SDK för bildsökning i Bing innehåller funktionerna i REST API:et för webbfrågor och parsning av resultat.

[Källkoden till exempel med SDK:t för videosökning i Bing med Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) är tillgänglig på Git Hub.


## <a name="application-dependencies"></a>Programberoenden
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under **Sök**.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Installera Python om du inte redan har det. SDK är kompatibelt med Python 2.7, 3.3, 3.4, 3.5 och 3.6.

Den allmänna rekommendationen för Python-utveckling är att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html). Installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv). Installera virtualenv för Python 2.7.
```
python -m venv mytestenv
```
Installera beroenden för SDK för videosökning i Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```
## <a name="video-search-client"></a>Klient för videosökning
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Lägg till importer:
```
subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Skapa en instans av `CognitiveServicesCredentials` och instantiera klienten:
```
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Kör en videosökning efter (SwiftKey) och kontrollera sedan antalet resultat. Skriv ut `ID`, `name` och `URL` för det första videoresultatet.
```
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

try:
    video_result = client.videos.search(query="SwiftKey")
    print("Search videos for query \"SwiftKey\"")

    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")

except Exception as err:
    print("Encountered exception. {}".format(err))

```
Kör en videosökning efter (Bellevue Trailer) som är kostnadsfri, kort och har en upplösning på 1080 bildpunkter. Kontrollera antalet resultat och skriv ut `ID`, `name` och `URL` för det första videoresultatet.
```
def video_search_with_filtering(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        video_result = client.videos.search(
            query="Bellevue Trailer",
            pricing=VideoPricing.free,  # Can use the str "free" too
            length=VideoLength.short,   # Can use the str "short" too
            resolution=VideoResolution.hd1080p  # Can use the str "hd1080p" too
        )
        print("Search videos for query \"Bellevue Trailer\" that is free, short and 1080p resolution")

        if video_result.value:
            first_video_result = video_result.value[0]
            print("Video result count: {}".format(len(video_result.value)))
            print("First video id: {}".format(first_video_result.video_id))
            print("First video name: {}".format(first_video_result.name))
            print("First video url: {}".format(first_video_result.content_url))
        else:
            print("Didn't see any video result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

Få populära resultat. Kontrollera kategorier och paneler för banderoller:
```
def video_trending(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_result = client.videos.trending()
        print("Search trending video")

        # Banner tiles
        if trending_result.banner_tiles:
            first_banner_tile = trending_result.banner_tiles[0]
            print("Banner tile count: {}".format(len(trending_result.banner_tiles)))
            print("First banner tile text: {}".format(first_banner_tile.query.text))
            print("First banner tile url: {}".format(first_banner_tile.query.web_search_url))
        else:
            print("Couldn't find banner tiles!")

        # Categorires
        if trending_result.categories:
            first_category = trending_result.categories[0]
            print("Category count: {}".format(len(trending_result.categories)))
            print("First category title: {}".format(first_category.title))
            if first_category.subcategories:
                first_subcategory = first_category.subcategories[0]
                print("Subcategory count: {}".format(len(first_category.subcategories)))
                print("First subcategory title: {}".format(first_subcategory.title))
                if first_subcategory.tiles:
                    first_tile = first_subcategory.tiles[0]
                    print("Subcategory tile count: {}".format(len(first_subcategory.tiles)))
                    print("First tile text: {}".format(first_tile.query.text))
                    print("First tile url: {}".format(first_tile.query.web_search_url))
                else:
                    print("Couldn't find subcategory tiles!")
            else:
                print("Couldn't find subcategories!")
        else:
            print("Couldn't find categories!")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Kör en videosökning efter (Bellevue Trailer) och sök sedan efter detaljerad information om den första videon.
```
def video_detail(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        video_result = client.videos.search(query="Bellevue Trailer")
        first_video_result = video_result.value[0]

        video_details = client.videos.details(
            query="Bellevue Trailer",
            id=first_video_result.video_id,
            modules=[VideoInsightModule.all]  # Can use ["all"] too
        )
        print("Search detail for video id={}, name={}".format(
            first_video_result.video_id,
            first_video_result.name
        ))

        if video_details.video_result:
            print("Expected Video id: {}".format(video_details.video_result.video_id))
            print("Expected Video name: {}".format(video_details.video_result.name))
            print("Expected Video url: {}".format(video_details.video_result.content_url))
        else:
            print("Couldn't find expected video")

        if video_details.related_videos.value:
            first_related_video = video_details.related_videos.value[0]
            print("Related video count: {}".format(len(video_details.related_videos.value)))
            print("First related video id: {}".format(first_related_video.video_id))
            print("First related video name: {}".format(first_related_video.name))
            print("First related video content url: {}".format(first_related_video.content_url))
        else:
            print("Couldn't find any related video!")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Nästa steg

[Exempel med Cognitive Services SDK för Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

