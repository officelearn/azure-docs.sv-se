---
title: 'Snabbstart: Skapa en miniatyrbild – REST, Python'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9962874600e259a639c70b7b180e5fc2a940461f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60401144"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-python-in-computer-vision"></a>Snabbstart: Generera en miniatyrbild med hjälp av REST-API:et och Python i Visuellt innehåll

I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av REST-API:et för visuellt innehåll. Med metoden [Hämta miniatyrbild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) kan du ange önskad höjd och bredd. Därefter använder Visuellt innehåll smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/try/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnadsfri utvärderingsversion nyckel från [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på visuellt innehåll och få din nyckel.
- En kodredigerare såsom [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Kopiera följande kod till kodredigeraren för att skapa och köra exemplet. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Gör därefter något av följande:
1. Ersätt värdet för `subscription_key` med din prenumerationsnyckel.
1. Ersätt värdet för `vision_base_url` med slutpunktsadressen för resursen för visuellt innehåll i den Azure-region där du fick dina prenumerationsnycklar, om så behövs.
1. Du kan också ersätta värdet för `image_url` med webbadressen till en annan bild som du vill generera en miniatyrbild för.
1. Spara koden som en fil med tillägget `.py`. Till exempel `get-thumbnail.py`.
1. Öppna ett kommandotolksfönster.
1. Kör exemplet i kommandotolken med kommandot `python`. Till exempel `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i form av binärdata som representerar bilddata för miniatyrbilden. Exemplet ska visa den här bilden. Om begäran misslyckas visas svaret i kommandotolkens fönster och bör innehålla en felkod.

## <a name="run-in-jupyter-optional"></a>Köra i Jupyter (valfritt)

Du kan välja att köra den här snabbstarten steg för steg med hjälp av en Jupyter-anteckningsbok i [MyBinder](https://mybinder.org). Starta Binder med den här knappen:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Nästa steg

Nu ska du få mer detaljerad information om funktionen för att skapa miniatyrbilder.

> [!div class="nextstepaction"]
> [Generera miniatyrer](../concept-generating-thumbnails.md)
