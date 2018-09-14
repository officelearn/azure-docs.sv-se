---
title: 'Snabbstart: Använda Python för att anropa API för webbsökning i Bing'
description: I den här snabbstarten lär du dig att göra ditt första anrop API för webbsökning i Bing med Python och att ta emot ett JSON-svar.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: cd53a323a07617284e82004a6b3feed57b6e15e2
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888615"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Snabbstart: Använda Python för att anropa API för webbsökning i Bing  

Använd den här snabbstarten för att göra ditt första anrop till API för webbsökning i Bing och få ett JSON-svar på mindre än 10 minuter.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

Det här exemplet körs som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org). Klicka på aktivitetsikonen för att starta Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

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
