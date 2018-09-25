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
ms.openlocfilehash: 3666e92372e9bed80e5c0c7991dcac730cebb588
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967601"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Anropa anpassad sökning i Bing slutpunkt (Python)

Den här snabbstarten visar hur du begär sökresultat från din anpassade Sökinstans med hjälp av Python för att anropa anpassad sökning i Bing-slutpunkten. 

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra den här snabbstarten:

- En färdiga att använda anpassad Sökinstans. Se [skapar din första Bing Custom Search-instans](quick-start.md).
- [Python](https://www.python.org/) installerad.
- En prenumerationsnyckel. Du kan få en prenumerationsnyckel när du aktiverar din [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), eller du kan använda en betald prenumerationsnyckel från instrumentpanelen i Azure (se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Skapa en mapp för din kod.  
  
2. Navigera till mappen som du nyss skapade från en administratörskommandotolk eller terminal.  
  
3. Installera den **begäranden** python-modulen:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Skapa en fil med namnet BingCustomSearch.py i den mapp du skapade och kopiera följande kod till den. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-anpassad-CONFIG-ID** med din subscriptioin nyckeln och konfigurationsdata-ID.  
  
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
  
7. Kör kod med följande kommando.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Nästa steg
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Använda decoration markörer för att markera text](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)
