---
title: 'Snabbstart: API för nyhetssökning i Bing, Python'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API:et för nyhetssökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: 2696401b15ceca86c7e7b7d33e7e7f7887f4b7dc
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164345"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Snabbstart för API:et för nyhetssökning i Bing med Python
Den här genomgången demonstrerar ett enkelt exempel på anrop till API:et för nyhetssökning i Bing och efterbearbetning av det resulterande JSON-objektet. Mer information finns i [dokumentationen för nyhetssökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Du kan köra det här exemplet som en Jupyter Notebook på [MyBinder](https://mybinder.org) genom att klicka på ikonen för att starta Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API:er för Bing-sökresultat**. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Du behöver den åtkomstnyckel som du fick när du aktiverade din kostnadsfria utvärderingsversion, eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure.

## <a name="running-the-walkthrough"></a>Köra genomgången
Ställ först in `subscription_key` till din API-nyckel för Bing API-tjänsten.


```python
subscription_key = None
assert subscription_key
```

Därefter kontrollerar du att `search_url`-slutpunkten är korrekt. När detta skrivs är används endast en slutpunkt för API:er för sökning i Bing. Om du stöter på auktoriseringsfel kontrollerar du detta värde mot slutpunkten för Bing-sökmotorn i Azure-instrumentpanelen.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Ange `search_term` för att söka efter nyhetsartiklar om Microsoft.


```python
search_term = "Microsoft"
```

Följande blocket använder `requests`-biblioteket i Python för att anropa API:er för sökning i Bing och returnera resultatet som ett JSON-objekt. Observera att vi skickar in API-nyckeln via `headers`-ordlistan och söktermen via `params`-ordlistan. En fullständig lista över alternativ som kan användas för att filtrera sökresultaten läser finns i dokumentation till [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results`-objektet innehåller de relevanta nyhetsartiklarna tillsammans med omfattande metadata. Till exempel extraherar följande rad med kod beskrivningarna i artiklarna.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Dessa beskrivningar kan sedan återges som en tabell med sökordet markerat i **fetstil**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Nyheter för sidindelning](paging-news.md)
> [Använda dekorationsmarkörer för att markera text](hit-highlighting.md)

## <a name="see-also"></a>Se även 

 [Söka på webben efter nyheter](search-the-web.md)  
 [Prova](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
