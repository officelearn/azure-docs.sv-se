---
title: 'Snabbstart: Videosökning i Bing, Python'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API:et för videosökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: e902ae940364810e634434fb1f08408fadea9c8a
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310556"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>Snabbstart: API för videosökning i Bing med Python

Den här genomgången beskriver hur du använder API:et för videosökning i Bing, som är en del av Microsoft Cognitive Services i Azure. Teknisk information om API:erna finns i [API-referensen](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).

Du kan köra det här exemplet som en Jupyter Notebook på [MyBinder](https://mybinder.org) genom att klicka på ikonen för att starta Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API:er för Bing-sökresultat**. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Du behöver den åtkomstnyckel som du fick när du aktiverade din kostnadsfria utvärderingsversion, eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure. Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="running-the-walkthrough"></a>Köra genomgången

Ställ först in `subscription_key` till din API-nyckel för Bing API-tjänsten.


```python
subscription_key = None
assert subscription_key
```

Därefter kontrollerar du att `search_url`-slutpunkten är korrekt. När detta skrivs är används endast en slutpunkt för API:er för sökning i Bing. Om du stöter på auktoriseringsfel kontrollerar du detta värde mot slutpunkten för Bing-sökmotorn i Azure-instrumentpanelen.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Ange `search_term` till att söka efter videor med kattungar


```python
search_term = "kittens"
```

Följande blocket använder `requests`-biblioteket i Python för att anropa API:er för Bing-sökresultat och returnera resultatet som ett JSON-objekt. Observera att vi skickar in API-nyckeln via `headers`-ordlistan och söktermen via `params`-ordlistan. En fullständig lista över alternativ som kan användas för att filtrera sökresultaten läser finns i dokumentation till [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results`-objektet innehåller de relevanta videorna tillsammans med omfattande metadata. Visa ett videoklipp genom att använda dess `embedHtml`-egenskap och infoga den i en `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Sidindelning av videor](paging-videos.md)
> [Ändra storlek på och beskära miniatyrbilder](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Se även 

 [Söka på webben efter videor](search-the-web.md) [Prova](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
