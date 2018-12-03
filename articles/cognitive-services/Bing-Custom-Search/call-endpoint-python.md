---
title: 'Snabbstart: Anropa slutpunkt med hjälp av Python – Anpassad sökning i Bing'
titlesuffix: Azure Cognitive Services
description: Den här snabbstarten visar hur du begär sökresultat från din anpassade sökinstans genom att använda Python för att anropa slutpunkten för Anpassad sökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: d7977f49a484665f133a119f491bd8986c0b6fc4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310727"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Snabbstart: Anropa slutpunkten för anpassad sökning i Bing (Python)

Den här snabbstarten visar hur du begär sökresultat från din anpassade sökinstans genom att använda Python för att anropa slutpunkten för Anpassad sökning i Bing. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

- En anpassad sökinstans som är redo att användas. Läs [Skapa din första instans för anpassad sökning i Bing](quick-start.md).
- [Python](https://www.python.org/) installerad.
- En prenumerationsnyckel. Du kan få en prenumerationsnyckel när du aktiverar din [kostnadsfria utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure (se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Skapa en mapp för din kod.  
  
2. Gå till mappen som du nyss skapade från en administratörskommandotolk eller terminal.  
  
3. Installera Python-modulen för **begäranden**:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Skapa en fil med namnet BingCustomSearch.py i mappen du skapade och kopiera följande kod till den. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-CUSTOM-CONFIG-ID** med din prenumerationsnyckel och ditt konfigurations-ID.  
  
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
  
7. Kör koden med följande kommando.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Nästa steg
- [Konfigurera värdbaserad UI-upplevelse](./hosted-ui.md)
- [Använda dekorationsmarkörer för att markera text](./hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
