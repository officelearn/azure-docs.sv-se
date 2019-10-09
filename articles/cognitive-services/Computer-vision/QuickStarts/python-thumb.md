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
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 042fc22e66924fb2ea1a6985f442c46061b7fc31
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176395"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Snabbstart: Generera en miniatyr med Visuellt innehåll REST API och python

I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av REST-API:et för visuellt innehåll. Med metoden [Hämta miniatyrbild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) kan du ange önskad höjd och bredd. Därefter använder Visuellt innehåll smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/try/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnads fri utvärderings nyckel från [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på visuellt innehåll och hämta din nyckel. Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och tjänst slut punkts strängen, med namnet `COMPUTER_VISION_SUBSCRIPTION_KEY` och `COMPUTER_VISION_ENDPOINT`.
- En kod redigerare som [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Kopiera följande kod till kodredigeraren för att skapa och köra exemplet. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v2.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Gör därefter något av följande:
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
