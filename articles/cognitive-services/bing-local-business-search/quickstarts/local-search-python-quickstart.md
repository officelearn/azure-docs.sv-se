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
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75379751"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Snabb start: skicka en fråga till API: et för lokal sökning i Bing i python

Använd den här snabb starten för att börja skicka begär anden till API: et för lokal sökning i Bing, som är en Azure-tjänst för inlärning. Även om det här enkla programmet skrivs i python är API: et en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

Det här exempel programmet hämtar lokala svars data från API: et för `hotel in Bellevue`Sök frågan.

## <a name="prerequisites"></a>Krav

* [Python](https://www.python.org/) 2. x eller 3. x
 
Du måste ha ett [COGNITIVE Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med Bing-API: er. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Använd den åtkomst nyckel som tillhandahålls av den kostnads fria utvärderings versionen.  Se även Priser för Cognitive Services –API för Bing-sökning.

## <a name="run-the-complete-application"></a>Kör hela programmet

Följande kod hämtar lokaliserade resultat. De implementeras i följande steg:
1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange Frågeparametern. 
3. Definiera den sökfunktion som skapar begäran och lägger till nyckeln Ocp-Apim-Subscription-Key.
4. Ange nyckeln Ocp-Apim-Subscription-Key. 
5. Upprätta anslutningen och skicka begäran.
6. Skriv ut JSON-resultaten.

Här följer den fullständiga koden för demon:

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
- [Snabb start för lokal Business search-nod](local-search-node-quickstart.md)
