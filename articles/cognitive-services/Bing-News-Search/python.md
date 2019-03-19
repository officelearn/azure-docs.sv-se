---
title: 'Snabbstart: Utföra en nyhetssökning med Python – REST-API för nyhetssökning i Bing'
titlesuffix: Azure Cognitive Services
description: Använd den här snabbstarten om du vill skicka en begäran till REST-API:et för nyhetssökning i Bing med hjälp av Python och få ett JSON-svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 7745db722d1862e5254c0e0fb07605799a8f7457
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076725"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Snabbstart: Utföra en nyhetssökning med hjälp av Python och REST-API:et för nyhetssökning i Bing

Använd den här snabbstarten för att göra ditt första anrop till API för nyhetssökning i Bing och få ett JSON-svar. Det här enkla JavaScript-programmet skickar en sökfråga till API:et och bearbetar resultatet. Även om det här programmet är skrivet i Python är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Du kan köra det här kodexemplet som en Jupyter Notebook på [MyBinder](https://mybinder.org) genom att klicka på ikonen för att starta Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Källkoden till det här exemplet finns även på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och importera begärandemodulen. Skapa variabler för din prenumerationsnyckel, en slutpunkt och ett sökvillkor. Du hittar slutpunkten på Azure-instrumentpanelen.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Skapa parametrar för begäran

1. Lägg till din prenumerationsnyckel i en ny ordlista med hjälp av `"Ocp-Apim-Subscription-Key"` som nyckel. Gör samma sak för sökparametrarna.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Skicka en begäran och få ett svar

1. Använd begärandebiblioteket för att anropa API för visuell sökning i Bing med hjälp av prenumerationsnyckeln och de ordlisteobjekt som skapades i föregående steg.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` innehåller svaret från API:et som ett JSON-objekt. Öppna beskrivningarna för den artikel som finns i svaret.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Visa resultaten

Dessa beskrivningar kan sedan återges som en tabell med sökordet markerat i **fetstil**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](tutorial-bing-news-search-single-page-app.md)
