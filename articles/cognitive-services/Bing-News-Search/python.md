---
title: Python Snabbstartsguide för Azure kognitiva-tjänster, Bing News Search API | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Bing News Sök API i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 0fde478b650513aa1527c1d47f5b453ba094506c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352353"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Snabbstart för Bing News Search API med Python
Den här genomgången visar ett enkelt exempel på anrop till Bing News Sök-API och efterbearbetning resulterande JSON-objekt. Mer information finns i [Bing ny sökning dokumentationen](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Du kan köra det här exemplet som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder badge: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

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
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Ange `search_term` att leta efter nyhetsartiklar om Microsoft.


```python
search_term = "Microsoft"
```

Följande blockera använder den `requests` bibliotek i Python att anropa till API: er för Bing-sökningen och returnerar resultatet som ett JSON-objekt. Observera att vi skicka in API-nyckel via den `headers` ordlista och sökningen term via den `params` ordlistan. Om du vill se en fullständig lista över alternativ som kan användas för att filtrera sökresultaten avser den [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) dokumentation.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Den `search_results` objektet innehåller de relevanta nya artiklarna tillsammans med omfattande metadata. Till exempel extraherar följande kodrad beskrivningar av artiklarna.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Dessa beskrivningar kan sedan återges som en tabell med sökord som markerats i **fetstil**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Nyheter för sidindelning](paging-news.md)
> [med decoration markörer Markera text](hit-highlighting.md)

## <a name="see-also"></a>Se också 

 [Söka på webben efter nyheter](search-the-web.md)  
 [Prova](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
