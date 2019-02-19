---
title: 'Snabbstart: Utföra en sökning med Python – API för webbsökning i Bing'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten om du vill skicka begäranden till REST-API:et för webbsökning i Bing med hjälp av Python och få ett JSON-svar
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: e191821c8e2c6e189e8d7f8befcb11009f2ed7d8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172373"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Snabbstart: Använda Python för att anropa API för webbsökning i Bing  

Använd den här snabbstarten för att göra ditt första anrop till API:et för webbsökning i Bing och få JSON-svaret. Det här Python-programmet skickar en sökbegäran till API:et och visar svaret. Även om det här programmet är skrivet i Python, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Det här exemplet körs som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org). Klicka på aktivitetsikonen för att starta Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 2.x eller 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definiera variabler

Ersätt värdet `subscription_key` med en giltig prenumerationsnyckel från ditt Azure-konto.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Deklarera slutpunkten för API för webbsökning i Bing. Om du får auktoriseringsfel kontrollerar du detta värde mot slutpunkten för Bing-sökmotorn i Azure-instrumentpanelen.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Du kan anpassa sökfrågan genom att ersätta värdet för `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Skapa en begäran

Det här blocket använder `requests`-biblioteket för att anropa API för webbsökning i Bing och returnerar resultatet som ett JSON-objekt. API-nyckeln skickas i `headers`-ordlistan, och söktermen samt frågeparametrarna skickas i `params`-ordlistan. Se dokumentationen till [API för webbsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) för en fullständig lista över alternativ och parametrar.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatera och visa svaret

`search_results`-objektet innehåller sökresultaten och metadata såsom relaterade frågor och sidor. Den här koden använder `IPython.display`-biblioteket för att formatera och visa svaret i webbläsaren.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Exempelkod på GitHub

Om du vill köra den här koden lokalt [finns det fullständiga exemplet på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie om app på en sida för Bing-webbsökning](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
