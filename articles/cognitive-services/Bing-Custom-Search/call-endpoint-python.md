---
title: Anropa slutpunkten med hjälp av kognitiva Python - Bing anpassad sökning - Microsoft-tjänster
description: Den här snabbstarten visar hur du begär sökresultat från anpassad sökning-instans med hjälp av Python för att anropa Bing anpassad sökning slutpunkten.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355734"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Anropa Bing anpassad sökning slutpunkt (Python)

Den här snabbstarten visar hur du begär sökresultat från anpassad sökning-instans med hjälp av Python för att anropa Bing anpassad sökning slutpunkten. 

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att slutföra den här snabbstarten:

- En anpassad sökning-instans. Se [skapa din första Bing anpassad sökning instans](quick-start.md).

-  [Python](https://www.python.org/) installerad.

- En [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing Search**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) är tillräcklig för den här snabbstarten. Du måste åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumeration nyckel från instrumentpanelen i Azure. 

## <a name="run-the-code"></a>Kör koden

Följ dessa steg för att anropa Bing anpassad sökning-slutpunkten:

1. Skapa en mapp för din kod.
2. Navigera till mappen som du skapade från en administratörskommandotolk eller terminal.
3. Installera den **begäranden** python-modul:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Skapa filen BingCustomSearch.py och kopiera följande kod till den.
8. Ersätt **din PRENUMERATION nyckel** och **din-anpassad-CONFIG-ID** med nyckeln och konfigurationsdata-ID (se steg 1).

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
- [Markera text med hjälp av decoration markörer](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)