---
title: 'Quickstart: Search for videos using the SDK for Python - Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att skicka sökförfrågningar om video med SDK:t Videosökning i Bing för Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 62f929f3428ef7534d833e54b0b8f492636b8a7e
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378760"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Quickstart: Perform a video search with the Bing Video Search SDK for Python

Använd den här snabbstarten till att börja söka efter nyheter med SDK:t Videosökning i Bing för Python. Även om Videosökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så är SDK:t ett enkelt sätt att integrera tjänsten i dina program. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) with additional annotations, and features.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Krav

- [Python](https://www.python.org/) 2.x eller 3.x
- SDK:t Videosökning i Bing för Python

Vi rekommenderar att du använder en [virtuell Python-miljö](https://docs.python.org/3/tutorial/venv.html). Du kan installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv). Installera virtualenv för Python 2.7 med:

```console
python -m venv mytestenv
```

Installera SDK:t Videosökning i Bing med:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigerare och följande importinstruktioner. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Skapa en variabel för din prenumerationsnyckel. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>Skapa sökklienten

Skapa en instans av `CognitiveServicesCredentials` och instantiera klienten:

```python
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Skicka en förfrågan och få ett svar

1. Använd `client.videos.search()` med sökfrågan för att skicka en förfrågan till API:t Videosökning i Bing och få ett svar.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Om svaret innehåller sökresultat tar du det första och skriver ut ID, namn och webbadress.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Se också 

- [Vad är API för videosökning i Bing?](../overview.md)
- [Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
