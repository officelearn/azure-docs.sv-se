---
title: 'Snabbstart: Begär och filtrera bilder med hjälp av SDK: N i Python'
description: I den här snabbstarten du begär och filtrera bilder som returneras av bildsökning i Bing, med hjälp av Python.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 4729f103bb9b50d4ff039907db8eb677f3dc290a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987647"
---
 # <a name="quickstart-request-and-filter-images-using-the-sdk-and-python"></a>Snabbstart: Begär och filtrera bilder med hjälp av SDK och Python

Sök SDK för Bing-bild innehåller funktionerna i REST-API för webbfrågor och parsa resultat. 

Den [källkoden för Search-SDK för Python Bing-bild exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) är tillgänglig på Git Hub.

## <a name="application-dependencies"></a>Programberoenden
Om du inte redan har det kan du installera Python. SDK: N är kompatibel med Python 2.7, 3.3, 3.4, 3.5 och 3.6.

Allmän rekommendation för Python-utveckling är att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html). Installera och initiera den virtuella miljön med den [venv modulen](https://pypi.python.org/pypi/virtualenv). Du måste installera virtuell miljö för Python 2.7.
```
python -m venv mytestenv
```
Installera SDK för Bing Search-avbildningen beroenden:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Sök-klienten för avbildning
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Search*. Lägg till import:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Skapa en instans av den `CognitiveServicesCredentials`, och skapa en instans av klienten:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Sök efter bilder på frågan (Yosemite), filtreras för animerade GIF-filer och hela. Kontrollera antalet resultat och skriva ut insightsToken, miniatyr-URL och Webb-URL: en för första resultatet.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
Sök avbildningar för (Yosemite), filtreras för animerade GIF-filer och hela.  Kontrollera antalet resultat.  Skriv ut `insightsToken`, `thumbnail url` och `web url` första resultatet.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

Få populära resultat:
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>Nästa steg

[Cognitive Services SDK för Python-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


