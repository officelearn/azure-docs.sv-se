---
title: Anropet och svar - Python Snabbstartsguide för Azure kognitiva tjänster, Bing Web Sök API | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Bing webb-API för sökning i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352437"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>Anropet och svar: första Bing webbsökning frågan i Python

Bing Web Sök API ger en upplevelse som liknar Bing.com/Search genom att returnera sökresultat som Bing avgör är relevanta för användarens fråga. Resultaten kan inkludera webbsidor, bilder, videor, nyheter och enheter, tillsammans med relaterade sökfrågor, stavfel, tidszoner, Enhetskonvertering, översättningar och beräkningar. Typerna av du får resultat baserat på deras relevans och nivån för Bing Search API: er som du prenumererar på.

Referera till den [API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) teknisk information om API: erna.

Du kan köra det här exemplet som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder badge: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>Förutsättningar
Du måste ha en [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing Search**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) är tillräcklig för den här snabbstarten. Du måste åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumeration nyckel från instrumentpanelen i Azure.

## <a name="running-the-walkthrough"></a>Kör den här genomgången

Ange `subscription_key` till API-nyckel för Bing-API-tjänsten.


```python
subscription_key = None
assert subscription_key
```

Därefter kontrollerar du att den `search_url` slutpunkten är korrekt. När detta skrivs används endast en slutpunkt för Bing search API: er. Om du stöter på fel auktorisering, kontrollera det här värdet mot Bing search slutpunkten i instrumentpanelen i Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Ange `search_term` frågan Bing för kognitiva Microsoft-tjänster.


```python
search_term = "Microsoft Cognitive Services"
```

Följande blockera använder den `requests` bibliotek i Python att anropa till API: er för Bing-sökningen och returnerar resultatet som ett JSON-objekt. Observera att vi skicka in API-nyckel via den `headers` ordlista och sökningen term via den `params` ordlistan. Om du vill se en fullständig lista över alternativ som kan användas för att filtrera sökresultaten avser den [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) dokumentation.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Den `search_results` objektet innehåller sökresultaten tillsammans med omfattande metadata, till exempel relaterade frågor och sidor. Följande rader med kod Formatera översta sidor som returneras av frågan.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Webbprogram för Bing search sida app självstudiekursen](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Se också 

[Översikt över Bing webbsökning](../overview.md)  
[Prova](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Hämta en kostnadsfri utvärderingsversion åtkomstnyckeln](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Bing Web Sök API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
