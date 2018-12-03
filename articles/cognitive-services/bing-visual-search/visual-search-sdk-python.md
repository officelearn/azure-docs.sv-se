---
title: 'Snabbstart: SDK för visuell sökning i Bing, Python'
titleSuffix: Azure Cognitive Services
description: Konfiguration av SDK:n för visuell sökning, SDK Python-konsolprogram.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9f2a6d9b75ccf704862d169b96ea1a1f2edb9815
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445695"
---
# <a name="quickstart-bing-visual-search-sdk-python"></a>Snabbstart: SDK för visuell sökning i Bing, Python

SDK för visuell sökning i Bing använder funktionen för REST API för webbegäranden och parsning av resultat.
[Källkoden för SDK-exempel för visuell sökning i Bing](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) är tillgänglig på Git Hub.

Kodscenarier dokumenteras under följande rubriker:
* [Klient för Visuell sökning](#client)
* [Slutfört konsolprogram](#complete-console)
* [Avbildning av binärt inlägg med cropArea](#binary-crop)
* [KnowledgeRequest-parameter](#knowledge-req)
* [Taggar, åtgärder och actionType](#tags-actions)

## <a name="application-dependencies"></a>Programberoenden
* För den här snabbstarten behöver du starta en prenumeration på S9-prisnivån enligt [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Så här startar du en prenumeration på Azure-portalen:
1. Ange ”BingSearchV7” i den textruta längst upp på Azure-portalen där det står `Search resources, services, and docs`.  
2. Under Marketplace i den nedrullningsbara listan väljer du `Bing Search v7`.
3. Ange `Name` för den nya resursen.
4. Välj `Pay-As-You-Go`-prenumeration.
5. Välj prisnivån `S9`.
6. Starta prenumerationen genom att klicka på `Enable`.
 
* Installera Python om du inte redan har det. SDK är kompatibelt med Python 2.7, 3.3, 3.4, 3.5 och 3.6.
* Den allmänna rekommendationen för Python-utveckling är att använda en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html). Installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv). Installera virtualenv för Python 2.7.
```
python -m venv mytestenv
```
Installera beroenden för SDK för visuell sökning i Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-visualsearch
```

<a name="client"></a> 
## <a name="visual-search-client"></a>Klient för visuell sökning
För att skapa en instans av `VisualSearchAPI`-klienten importerar du följande bibliotek:
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
Ersätt strängvärdet subscriptionKey med en giltig prenumerationsnyckel.
```
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
```
Instansiera sedan klienten:
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```
Du kan använda klienten för att söka efter avbildningar och tolka resultaten:
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
## <a name="complete-console-application"></a>Slutföra konsolprogram

Följande konsolprogram kör den tidigare definierade frågan och parsar resultatet:
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

Bing-sökexemplen visar olika funktioner i SDK:n.  Lägg till följande funktioner till den tidigare definierade `VisualSrchSDK`-klassen.

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>Avbildning av binärt inlägg med cropArea

Följande kod skickar en binäravbildning i brödtexten i post-begäran, tillsammans med ett cropArea-objekt.  Därefter skrivs imageInsightsToken, antalet taggar, antalet åtgärder och den första actionType ut.

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
## <a name="knowledgerequest-parameter"></a>KnowledgeRequest-parameter

Följande kod skickar en avbildnings-URL i parametern `knowledgeRequest` tillsammans med ett \"site:pinterest.com\"-filter. Därefter skrivs `imageInsightsToken`, antalet taggar, antalet åtgärder och den första actionType ut.
```
def search_url_with_filters(client_in, sub_key):

    client = client_in

    image_url = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80"
    filters = Filters(site="pinterest.com")

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
## <a name="tags-actions-and-actiontype"></a>Taggar, åtgärder och actionType

Följande kod skickar en avbildningsinsiktstoken i parametern knowledgeRequest tillsammans med ett cropArea-objekt. Därefter skrivs imageInsightsToken, antalet taggar, antalet åtgärder och den första actionType ut.

```
    client = client_in

    image_insights_token = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D"
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

[Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).