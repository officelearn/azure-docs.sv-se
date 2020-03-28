---
title: 'Snabbstart: Anropa din anpassade Bing-sökpunkt med Python | Microsoft-dokument'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att börja begära sökresultat från instansen av anpassad Bing-sökning med hjälp av Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: a601b309d18e489f6b631cb26e5f3e13ef790b42
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238835"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Snabbstart: Anropa din anpassade Bing-sökpunkt med Python

Använd den här snabbstarten till att börja begära sökresultat från din instans av anpassad Bing-sökning. Även om det här programmet är skrivet i Python, är API:et för anpassad Bing-sökning en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden för det här exemplet är tillgänglig på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Krav

- En instans av anpassad Bing-sökning. Se [Snabbstart: Skapa din första förekomst av anpassad bing-sökning](quick-start.md) för mer information.
- [Python (svenska)](https://www.python.org/) 2.x eller 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och lägg till följande importinstruktioner. Skapa variabler för prenumerationsnyckeln, ID för anpassad konfiguration och en sökterm. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran 

1. Konstruera en begäran-URL genom att lägga till söktermen i `q=`-frågeparametern och sökinstansens ID för anpassad konfiguration i `customconfig=`. Avgränsa parametrarna med ett `&`-tecken. Du kan använda den globala slutpunkten nedan eller den [anpassade underdomänslutpunkten](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure-portalen för din resurs.

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
