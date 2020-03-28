---
title: Snabbstart - Skicka en fråga till API:et i Python - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att börja använda API:et för lokal företagssökning i Bing i Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379751"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Snabbstart: Skicka en fråga till API:et för lokal företagssökning i Python

Använd den här snabbstarten för att börja skicka begäranden till API:et för sökning i Bing lokalt företag, som är en Azure Cognitive Service. Även om detta enkla program är skrivet i Python, är API en RESTful webbtjänst kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON.

Det här exemplet programmet hämtar lokala svarsdata `hotel in Bellevue`från API för sökfrågan .

## <a name="prerequisites"></a>Krav

* [Python (svenska)](https://www.python.org/) 2.x eller 3.x
 
Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med API:er för Bing. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Använd åtkomstnyckeln som tillhandahålls av den kostnadsfria utvärderingsversionen.  Se även Priser för Cognitive Services –API för Bing-sökning.

## <a name="run-the-complete-application"></a>Kör hela programmet

Följande kod får lokaliserade resultat. De implementeras i följande steg:
1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange frågeparametern. 
3. Definiera den sökfunktion som skapar begäran och lägger till nyckeln Ocp-Apim-Subscription-Key.
4. Ange nyckeln Ocp-Apim-Subscription-Key. 
5. Gör anslutningen och skicka begäran.
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
- [Snabbstart för lokal företagssökning](local-search-java-quickstart.md)
- [Snabbstart för lokal företagssökning C#](local-quickstart.md)
- [Snabbstart för söknod för lokalt företag](local-search-node-quickstart.md)
