---
title: Snabbstart för Bing Visual Search Python-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: debc5130824a0f661a3736b8d24d950fc789f2f3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550004"
---
Använd den här snabbstarten för att börja hämta bildinsikter från Bing Visual Search-tjänsten med hjälp av Python-klientbiblioteket. Bing Visual Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

[Exempel](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/visualsearch?view=azure-python) |  | [på](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/) referensdokumentation | [bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-visualsearch)[(PyPi)](https://pypi.org/project/azure-cognitiveservices-search-visualsearch/)

## <a name="prerequisites"></a>Krav

* [Python (svenska)](https://www.python.org/) 2.x eller 3.x
* Det rekommenderas att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html). Installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv).
* Klientbiblioteket Bing Visual Search för Python. Du kan installera det genom att köra följande kommandon:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och lägg till följande importinstruktioner. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Skapa variabler för din prenumerationsnyckel, ID för anpassad konfiguration och den bild du vill överföra. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Instansiera klienten

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>Skicka sökbegäran

1. Med avbildningen öppen, serialiserar du `VisualSearchRequest()` och skickar den som parametern `knowledge_request` för `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Om några resultat returnerades så skriver du ut dem, taggarna och åtgärderna i den första taggen.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../../tutorial-bing-visual-search-single-page-app.md)
