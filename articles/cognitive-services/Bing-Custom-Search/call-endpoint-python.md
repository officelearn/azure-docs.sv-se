---
title: 'Snabbstart: Anropa slutpunkten för anpassad Bing-sökning med hjälp av Python | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att börja begära sökresultat från instansen av anpassad Bing-sökning med hjälp av Python
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: 9534a60e66f194bf653e1bfd28d6d6f2a96ba90a
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558727"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Snabbstart: Anropa slutpunkten för API för anpassad Bing-sökning med hjälp av Python

Använd den här snabbstarten till att börja begära sökresultat från instansen av anpassad Bing-sökning. Även om det här programmet är skrivet i Python, är API:et för anpassad Bing-sökning en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden till det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En instans av anpassad Bing-sökning. Gå till [Snabbstart: Skapa din första instans av anpassad Bing-sökning](quick-start.md) för mer information.
- [Python](https://www.python.org/) 2.x eller 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigerare och följande importinstruktioner. Skapa variabler för prenumerationsnyckeln, ID för anpassad konfiguration och en sökterm. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran 

1. Konstruera en begäran-URL genom att lägga till söktermen i `q=`-frågeparametern och sökinstansens ID för anpassad konfiguration i `customconfig=`. Avgränsa parametrarna med ett `&`-tecken. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Skicka begäran till instansen av anpassad Bing-sökning och skriv ut det returnerade sökresultatet.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)