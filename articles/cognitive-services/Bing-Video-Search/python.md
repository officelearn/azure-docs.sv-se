---
title: Python Snabbstartsguide för Azure kognitiva-tjänster, Bing Video Sök API | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Bing Video Sök API i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: ce4356f05e69540bc3bc3241e2ec1751ff7a7276
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352341"
---
# <a name="quickstart-for-bing-video-search-api-with-python"></a>Snabbstart för Bing Video sökning API med Python

Den här genomgången visar hur du använder API Bing Video sökning, en del av Microsoft kognitiva Services på Azure. Referera till den [API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) teknisk information om API: erna.

Du kan köra det här exemplet som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder badge: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Förutsättningar
Du måste ha en [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing Search**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) är tillräcklig för den här snabbstarten. Du måste åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumeration nyckel från instrumentpanelen i Azure.

## <a name="running-the-walkthrough"></a>Kör den här genomgången

Ange först `subscription_key` till API-nyckel för Bing-API-tjänsten.


```python
subscription_key = None
assert subscription_key
```

Därefter kontrollerar du att den `search_url` slutpunkten är korrekt. När detta skrivs används endast en slutpunkt för Bing search API: er. Om du stöter på fel auktorisering, kontrollera det här värdet mot Bing search slutpunkten i instrumentpanelen i Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Ange `search_term` att leta efter videor för kattungar


```python
search_term = "kittens"
```

Följande blockera använder den `requests` bibliotek i Python att anropa till API: er för Bing-sökningen och returnerar resultatet som ett JSON-objekt. Observera att vi skicka in API-nyckel via den `headers` ordlista och sökningen term via den `params` ordlistan. Om du vill se en fullständig lista över alternativ som kan användas för att filtrera sökresultaten avser den [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) dokumentation.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Den `search_results` objektet innehåller de relevanta videorna tillsammans med omfattande metadata. Visa en videor, använda dess `embedHtml` egenskapen och infoga det i en `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Sidindelning videor](paging-videos.md)
> [Resizing och Beskär miniatyrbilder](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Se också 

 [Söka på webben efter videor](search-the-web.md) [prova](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
