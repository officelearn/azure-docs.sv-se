---
title: Anropa slutpunkten med Python - anpassad sökning i Bing – Microsoft Cognitive Services
description: Den här snabbstarten visar hur du begär sökresultat från din anpassade Sökinstans genom att använda Python för att anropa anpassad sökning i Bing-slutpunkten.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "35355734"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Anropa anpassad sökning i Bing slutpunkt (Python)

Den här snabbstarten visar hur du begär sökresultat från din anpassade Sökinstans genom att använda Python för att anropa anpassad sökning i Bing-slutpunkten. 

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att slutföra den här snabbstarten:

- En anpassad Sökinstans. Se [skapar din första Bing Custom Search-instans](quick-start.md).

-  [Python](https://www.python.org/) installerad.

- En [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing-sökresultat**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) är tillräcklig för den här snabbstarten. Du behöver åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumerationsnyckel från instrumentpanelen i Azure. 

## <a name="run-the-code"></a>Kör koden

Följ dessa steg för att anropa Bing Custom Search-slutpunkten:

1. Skapa en mapp för din kod.
2. Navigera till mappen som du nyss skapade från en administratörskommandotolk eller terminal.
3. Installera den **begäranden** python-modulen:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Skapa filen BingCustomSearch.py och kopiera följande kod till den.
8. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-anpassad-CONFIG-ID** med ditt ID för nyckeln och konfigurationsdata (se steg 1).

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. Kör kod med följande kommando.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Nästa steg
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Använda decoration markörer för att markera text](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)