---
title: Visual Sök SDK Python Quickstart | Microsoft Docs
description: Installationsprogrammet för Visual Sök Python SDK-konsolprogram.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: f7a1f275f9059abdceaef577fb5ca722c9951366
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939480"
---
# <a name="visual-search-sdk-python-quickstart"></a>Visual Sök SDK Python Snabbstart

Bing Visual Sök SDK använder funktionerna i REST-API för webbegäranden och tolkning resultat.
Den [källkoden för Python Bing Visual Sök SDK-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) är tillgänglig på Git-hubben.

Koden scenarier dokumenteras under följande rubriker:
* [Visual Sök-klient](#client)
* [Fullständig konsolprogram](#complete-console)
* [Bild binära post med cropArea](#binary-crop)
* [KnowledgeRequest parameter](#knowledge-req)
* [Taggar, åtgärder och åtgärdstyp](#tags-actions)

## <a name="application-dependencies"></a>Programberoenden
* En kognitiva services API-nyckel krävs för att autentisera SDK-anrop. Registrera dig för en [kostnadsfri utvärderingsversion nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=search-api-v7). Utvärderingsversion nyckeln är bra för sju dagar med 1 anrop per sekund. Produktion scenariot [köpa åtkomstnyckel](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7). Se även [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/visual/).
* Om du inte redan har det installera Python. SDK är kompatibelt med Python 2.7, 3.3, 3.4, 3.5 och 3,6.
* Allmänna rekommendationer för utveckling av Python är att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html). Installera och starta den virtuella miljön med den [venv modulen](https://pypi.python.org/pypi/virtualenv). Installera virtuell miljö för Python 2.7.
```
python -m venv mytestenv
```
Installera SDK för Visual Sök i Bing beroenden:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-visualsearch
```

<a name="client"></a> 
## <a name="visual-search-client"></a>Visual Sök-klient
Att skapa en instans av den `VisualSearchAPI` klient och importera följande bibliotek:
```
import http.client, urllib.parse
import json
import os.path
from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,
)
```
Ersätt strängvärdet subscriptionKey med giltig prenumeration-nyckel.
```
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
```
Sedan skapa en instans av klienten:
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```
Använd klienten för att söka bilder och tolka resultat:
```
PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
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

<a name="complete-console"></a> 
## <a name="complete-console-application"></a>Fullständig konsolprogram

Följande konsolprogrammet utförs tidigare definierade frågan och Parsar resultaten:
```
import http.client, urllib.parse
import json
import os.path

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'

PATH = 'C:\\Users\\v-gedod\\azure-cognitive-samples\\mytestenv\\TestImages\\'

from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,)

from msrest.authentication import CognitiveServicesCredentials

client = VisualSearchAPI(CognitiveServicesCredentials(subscription_key))

image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
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


# Uncomment these methods to include code under the following headings of this documentation.
#search_image_binary_with_crop_area(client, subscription_key, PATH)
#search_url_with_filters(client, subscription_key)
#search_insights_token_with_crop_area(client, subscription_key)

```

Bing search exemplen visar olika funktioner i SDK.  Lägg till följande funktioner i den tidigare definierad `VisualSrchSDK` klass.

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>Bild binära post med cropArea

Följande kod skickar en bild binära i brödtexten för post-begäran, tillsammans med ett cropArea-objekt.  Sedan ut imageInsightsToken, antal taggar, antalet åtgärder och den första åtgärdstypen.

```
def search_image_binary_with_crop_area(client, sub_key, file_path):

    #client = VisualSearchAPI(CognitiveServicesCredentials(sub_key))

    image_path = os.path.join(file_path, "image.jpg")
    with open(image_path, "rb") as image_fd:

        crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)
        knowledge_request = VisualSearchRequest(image_info=ImageInfo(crop_area=crop_area))

        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(knowledge_request.serialize())

        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)

        if not result:
            print("No visual search result data.")
            return

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
<a name="knowledge-req"></a>
## <a name="knowledgerequest-parameter"></a>KnowledgeRequest parameter

Följande kod skickar en bild-url i den `knowledgeRequest` parameter, tillsammans med en \"plats: www.bing.com\" filter. Sedan ut den `imageInsightsToken`, antal taggar, antalet åtgärder och den första åtgärdstypen.
```
def search_url_with_filters(client_in, sub_key):

    client = client_in

    image_url = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80"
    filters = Filters(site="www.bing.com")

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(url=image_url),
        knowledge_request=KnowledgeRequest(filters=filters)
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with url of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

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
<a name="tags-actions"></a>
## <a name="tags-actions-and-actiontype"></a>Taggar, åtgärder och åtgärdstyp

Följande kod skickar en bild insikter token i parametern knowledgeRequest tillsammans med ett cropArea-objekt. Sedan ut imageInsightsToken, antal taggar, antalet åtgärder och den första åtgärdstypen.

```
    client = client_in

    image_insights_token = "bcid_113F29C079F18F385732D8046EC80145*ccid_oV/QcH95*mid_687689FAFA449B35BC11A1AE6CEAB6F9A9B53708*thid_R.113F29C079F18F385732D8046EC80145"
    crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(
            image_insights_token=image_insights_token,
            crop_area=crop_area
        ),
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with URL of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

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

[Kognitiva services .NET SDK-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).