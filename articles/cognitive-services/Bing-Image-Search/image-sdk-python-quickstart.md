---
title: Bild Sök SDK Python quickstart | Microsoft Docs
description: Installationsprogrammet för avbildningen Sök SDK-konsolprogram.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355248"
---
# <a name="image-search-sdk-python-quickstart"></a>Bild Sök SDK Python Snabbstart

Bing avbildningen Sök SDK innehåller funktionerna i REST API för webb-frågor och tolkning resultat. 

Den [källkoden för Python Bing avbildningen Sök SDK-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) är tillgänglig på Git-hubben.

## <a name="application-dependencies"></a>Programberoenden
Om du inte redan har det installera Python. SDK är kompatibelt med Python 2.7, 3.3, 3.4, 3.5 och 3,6.

Allmänna rekommendationer för utveckling av Python är att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html). Installera och starta den virtuella miljön med den [venv modulen](https://pypi.python.org/pypi/virtualenv). Du måste installera virtuell miljö för Python 2.7.
```
python -m venv mytestenv
```
Installera Bing avbildningen Sök SDK beroenden:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Klienten för avbildning sökning
Hämta en [kognitiva åtkomstnyckeln](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Lägg till importen:
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
Söka bilder på frågan (Yosemite), filtreras för animerad GIF-filer och hela. Kontrollera antalet resultat och skriva ut insightsToken miniatyrbildens URL och web Webbadressen till första resultat.
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
Sök avbildningar för (Yosemite), filtreras för animerad GIF-filer och hela.  Kontrollera antalet resultat.  Skriva ut `insightsToken`, `thumbnail url` och `web url` första resultat.
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

Ger trender resultat:
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

[Kognitiva Services Python SDK-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


