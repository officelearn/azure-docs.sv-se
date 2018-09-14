---
title: 'Snabbstart: Sök efter bilder med hjälp av SDK för Bing Search-avbildningen och Python'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att söka efter bilder på webben med hjälp av SDK för Bing Search-avbildningen och Python.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 4a24f1e4e051b627034f1d4664e94e0f47c43014
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578301"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-python"></a>Snabbstart: Sök efter bilder med SDK för Bing Search-avbildningen och Python

Använd den här snabbstarten för att göra sökningen första avbildning med hjälp av Bing-bild Search SDK, som är en Omslutning för API: et och innehåller samma funktioner. Den här enkla Python-program skickar en sökfråga för avbildningen, Parsar JSON-svar och visar Webbadressen till den första bilden som returneras.

Källkoden för det här exemplet finns [på Github](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) med ytterligare felhantering och anteckningar.

## <a name="prerequisites"></a>Förutsättningar 

* [Python 2.7 eller 3.4](https://www.python.org/) och högre.

* Den [Azure bildsökning SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) för Python
    * Installera med hjälp av `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera programmet

1. Skapa ett nytt pythonskript i din favorit-IDE eller redigerare och följande importer:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Skapa variabler för din prenumerationsperiod för nyckeln och sökning.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Skapa avbildning search-klient

3. Skapa en instans av `CognitiveServicesCredentials`, och använda den för att skapa en instans av klienten:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
4. Skicka en sökfråga till sökning i Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
## <a name="process-and-view-the-results"></a>Bearbeta och visa resultaten

Parsa bild resultatet som returneras i svaret.


Om svaret innehåller sökresultat, lagra det första resultatet och skriva ut information om, till exempel en miniatyrbild URL, den ursprungliga URL: en, tillsammans med det totala antalet returnerade avbildningar.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bildsökning i Bing ensidesapp självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Se också 

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en online Interaktiv demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Hämta en kostnadsfri Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Python-exempel för Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)