---
title: Anropet och svar - Python Snabbstartsguide för Azure kognitiva tjänster, Bing avbildningen Sök API | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Bing avbildningen Sök-API i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 3b5d6a961ce4bcde8aaf73f1fbd30689a6c2c2d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352347"
---
# <a name="call-and-response-your-first-bing-image-search-query-in-python"></a>Anropet och svar: första Bing avbildningen sökfrågan i Python

Bing avbildningen Sök API ger en upplevelse som liknar Bing.com/Images genom att låta dig skicka en sökfråga användaren till Bing och få tillbaka en lista över relevanta bilder.

Den här genomgången visar ett enkelt exempel på anrop till Bing avbildningen Sök-API och efterbearbetning resulterande JSON-objekt. Mer information finns i [Bing Image-sökning dokumentationen](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

Du kan köra det här exemplet som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder badge: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing Search**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) är tillräcklig för den här snabbstarten. Du måste åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumeration nyckel från instrumentpanelen i Azure.

## <a name="running-the-walkthrough"></a>Kör den här genomgången
Om du vill fortsätta med den här genomgången, ange `subscription_key` till API-nyckel för Bing-API-tjänsten.


```python
subscription_key = None
assert subscription_key
```

Därefter kontrollerar du att den `search_url` slutpunkten är korrekt. När detta skrivs används endast en slutpunkt för Bing search API: er. Om du stöter på fel auktorisering, kontrollera det här värdet mot Bing search slutpunkten i instrumentpanelen i Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Ange `search_term` att leta efter avbildningar av Valpar.


```python
search_term = "puppies"
```

Följande blockera använder den `requests` bibliotek i Python att anropa till API: er för Bing-sökningen och returnerar resultatet som ett JSON-objekt. Observera att vi skicka in API-nyckel via den `headers` ordlista och sökningen term via den `params` ordlistan. Om du vill se en fullständig lista över alternativ som kan användas för att filtrera sökresultaten avser den [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentation.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Den `search_results` objektet innehåller själva bilderna tillsammans med omfattande metadata som relaterade objekt. Följande kodrad kan exempelvis extrahera miniatyren URL: er för de första 16 resultaten.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Vi kan sedan använda den `PIL` biblioteket för att hämta miniatyrbilder och `matplotlib` biblioteket för att göra dem på ett rutnät som $ $4 \times 4.


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
> [Bing Image-sökning sida app självstudiekursen](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se också 

[Översikt över Bing Image-sökning](../overview.md)  
[Prova](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Hämta en kostnadsfri utvärderingsversion snabbtangent](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Bing avbildningen Sök API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
