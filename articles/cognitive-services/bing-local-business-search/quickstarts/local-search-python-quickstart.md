---
title: 'Snabb start – skicka en fråga till API: et i python-Bing lokal företags sökning'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att börja använda Bing-API för lokal sökning i python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: fcd3ab3cce74aa0ef021427904077c364de1f493
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84606283"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Snabb start: skicka en fråga till API: et för lokal sökning i Bing i python

Använd den här snabb starten för att lära dig hur du skickar begär anden till API: et för lokal sökning i Bing, som är en Azure-tjänst för inlärning. Även om det här enkla programmet skrivs i python är API: et en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

Det här exempel programmet hämtar lokala svars data från API: et för en Sök fråga.

## <a name="prerequisites"></a>Krav

* [Python](https://www.python.org/) 2. x eller 3. x.
* Ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med API:er för Bing-sökresultat. I den här snabb starten räcker den [kostnads fria utvärderings versionen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) . Spara API-nyckeln som anges när du aktiverar din kostnads fria utvärderings version. Mer information finns i [Cognitive Services priser – Bing-sökning API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Kör hela programmet

Följande exempel hämtar lokaliserade resultat, som implementeras i följande steg:
1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange Frågeparametern. 
3. Definiera Sök funktionen som skapar begäran och lägger till `Ocp-Apim-Subscription-Key` rubriken.
4. Ange `Ocp-Apim-Subscription-Key` sidhuvudet. 
5. Upprätta anslutningen och skicka begäran.
6. Skriv ut JSON-resultaten.

Den fullständiga koden för den här demon är följande:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Nästa steg
- [Lokal affärs sökning Java snabb start](local-search-java-quickstart.md)
- [Snabb start för lokal affärs sökning i C#](local-quickstart.md)
- [Snabb start för lokal Business search Node. js](local-search-node-quickstart.md)
