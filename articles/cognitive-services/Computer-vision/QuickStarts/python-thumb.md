---
title: 'Snabbstart: Skapa en miniatyrbild – REST, Python – Visuellt innehåll'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och Python.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: c5809b3dd62d87e2a1f3bde762d17bef6d5732ae
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632933"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-python-in-computer-vision"></a>Snabbstart: Generera en miniatyrbild med hjälp av REST-API:t och Python i Visuellt innehåll

I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av REST-API:t i Visuellt innehåll. Med metoden [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) kan du generera en miniatyrbild av en bild. Du anger höjden och bredden, som kan skilja sig från den ursprungliga bildens proportioner. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

Du kan köra den här snabbstarten steg för steg med hjälp av en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org). Starta Binder med den här knappen:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att använda Visuellt innehåll behöver du en prenumerationsnyckel. Mer information finns i avsnittet [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Hämta prenumerationsnycklar).

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha [Python](https://www.python.org/downloads/) installerat om du vill köra exemplet lokalt.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Skaffa prenumerationsnycklar](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Så här skapar du och kör exemplet:

1. Kopiera följande kod till en textredigerarere.
1. Gör följande ändringar i koden när så behövs:
    1. Ersätt värdet för `subscription_key` med din prenumerationsnyckel.
    1. Ersätt värdet för `vision_base_url` med slutpunktsadressen för resursen för visuellt innehåll i den Azure-region där du fick dina prenumerationsnycklar, om så behövs.
    1. Du kan också ersätta värdet för `image_url` med webbadressen till en annan bild som du vill generera en miniatyrbild för.
1. Spara koden som en fil med tillägget `.py`. Till exempel `get-thumbnail.py`.
1. Öppna ett kommandotolksfönster.
1. Kör exemplet genom att använda kommandot `python` i kommandotolken. Till exempel `python get-thumbnail.py`.

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
# Free trial subscription keys are generated in the westcentralus region.
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

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i form av binärdata som representerar bilddata för miniatyrbilden. Om begäran lyckas så genereras miniatyrbilden från binärdata i svaret och visas i exemplet. Om begäran misslyckas visas svaret i kommandotolkens fönster. Svaret för en misslyckad begäran innehåller en felkod och ett meddelande som hjälper dig att avgöra vad som gick fel.

## <a name="next-steps"></a>Nästa steg

Utforska ett Python-program som använder Visuellt innehåll för att utföra optisk teckenläsning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriv visuella funktioner, inklusive ansikten, i en bild. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Självstudie: API för visuellt innehåll med Python](../Tutorials/PythonTutorial.md)
