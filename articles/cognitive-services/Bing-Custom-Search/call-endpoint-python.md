---
title: 'Snabb start: anropa din Anpassad sökning i Bing slut punkt med python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att börja begära Sök Resultat från Anpassad sökning i Bing-instansen med python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: e1248a4e90653f0e862841ec6f58a12364943590
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196566"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Snabb start: anropa din Anpassad sökning i Bing slut punkt med python

Använd den här snabb starten för att lära dig hur du begär Sök Resultat från Anpassad sökning i Bing-instansen. Även om det här programmet är skrivet i python är API för anpassad Bing-sökning en webb tjänst för RESTful som är kompatibel med de flesta programmeringsspråk. Käll koden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Förutsättningar

- En instans av anpassad Bing-sökning. Mer information finns i [snabb start: skapa din första anpassad sökning i Bing-instans](quick-start.md).
- [Python](https://www.python.org/) 2. x eller 3. x.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

- Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och lägg till följande importinstruktioner. Skapa variabler för din prenumerations nyckel, anpassat konfigurations-ID och Sök villkor.

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran 

1. Konstruera URL: en för begäran genom att lägga till sökordet i `q=` Frågeparametern och Sök instansens anpassade konfigurations-ID till `customconfig=` parametern. Avgränsa parametrarna med ett et-tecken ( `&` ). Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Skicka begäran till din Anpassad sökning i Bing-instans och skriv ut de returnerade Sök resultaten.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)
