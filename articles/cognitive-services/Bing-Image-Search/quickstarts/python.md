---
title: 'Snabbstart: Skicka sökfrågor med hjälp av REST-API för sökning i Bing i Python'
description: I den här snabbstarten har skickar du sökfrågor till Bing Search API att hämta en lista över relevanta avbildningar med hjälp av Python.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987472"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Snabbstart: Skicka sökfrågor med hjälp av REST API och Python

Sökning i Bing ger en upplevelse som liknar Bing.com/Images genom att låta dig skicka en sökfråga för användaren till Bing och få tillbaka en lista över relevanta avbildningar.

Den här genomgången visar ett enkelt exempel på anrop till sökning i Bing och efterbearbeta för ändring av det resulterande JSON-objektet. Mer information finns i [bildsökning i Bing dokumentation](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

Du kan köra det här exemplet som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder ge en skylt: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>Kör den här genomgången
Om du vill fortsätta med den här genomgången, ange `subscription_key` till din API-nyckel för API för Bing-tjänsten.


```python
subscription_key = None
assert subscription_key
```

Därefter kontrollerar du att den `search_url` slutpunkten är korrekt. När detta skrivs är används endast en slutpunkt för Bing search API: er. Om det uppstår auktoriseringsfel kan kontrollera detta värde mot Bing search-slutpunkten i instrumentpanelen i Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Ange `search_term` du söker efter bilder av Valpar.


```python
search_term = "puppies"
```

Följande blockera använder den `requests` biblioteket i Python för att anropa till Bing search API: er och returnerar resultatet som ett JSON-objekt. Observera att vi skickar in API-nyckel via den `headers` ordlista och sökningen term via den `params` ordlista. Om du vill se en fullständig lista över alternativ som kan användas för att filtrera sökresultaten, referera till den [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentation.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Den `search_results` objektet innehåller de faktiska avbildningarna tillsammans med omfattande metadata, t.ex relaterade objekt. Följande rad med kod kan exempelvis extrahera miniatyr URL: er för de första 16 resultaten.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Vi kan sedan använda den `PIL` -biblioteket för att ladda ned miniatyrbilder och `matplotlib` biblioteket för att visa dem på ett $4 \times 4$ rutnät.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bildsökning i Bing ensidesapp självstudien](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se också 

[Bildsökning i Bing-översikt](../overview.md)  
[Prova](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Hämta en kostnadsfri utvärderingsversion åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
