---
title: 'Snabbstart: Project Answer Search, Python'
titlesuffix: Azure Cognitive Services
description: Python-exempel för att komma igång med Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 2eaf07e041998efade1091861144a2dc4d78c56d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721546"
---
# <a name="quickstart-project-answer-search-with-python"></a>Snabbstart för Project Answer Search med Python

I följande Python-exempel skapas och skickas en begäran om information om ”Gibraltarklippan”.

## <a name="prerequisites"></a>Nödvändiga komponenter

Få en åtkomstnyckel för den kostnadsfria utvärderingsversionen av [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

I det här exemplet används Python 3.6.4

## <a name="code-scenario"></a>Kodscenario 

Följande kod skapar en URL-förhandsgranskning.
Den implementeras i följande steg:
1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange den fråge-URL som ska förhandsgranskas och lägg till frågeparametern.  
3. Ange frågeparametern.
4. Definiera den sökfunktion som skapar begäran och lägger till nyckeln *Ocp-Apim-Subscription-Key*.
5. Ange nyckeln *Ocp-Apim-Subscription-Key*. 
6. Upprätta anslutningen och skicka begäran.
7. Skriv ut JSON-resultaten.

Här följer den fullständiga koden för demon:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

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
- [Snabbstart för C#](c-sharp-quickstart.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för Node](node-quickstart.md)
