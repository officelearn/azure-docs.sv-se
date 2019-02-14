---
title: 'Snabbstart: Förhandsversion av projekt-URL, Python'
titlesuffix: Azure Cognitive Services
description: Skriptexempel för att snabbt komma igång med att använda förhandsgranskningen av projekt-URL med Python.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: afb64b799e826abc3e6aad661a97211b43304dc6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873282"
---
# <a name="quickstart-url-preview-with-python"></a>Snabbstart: URL-förhandsgranskning med Python

Följande Python-exempel skapar en URL-förhandsgranskning för webbplatsen SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Nödvändiga komponenter

Få en åtkomstnyckel för den kostnadsfria utvärderingsversionen av [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

I det här exemplet används Python 3.6.

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
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](csharp.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för JavaScript](javascript.md)
- [Snabbstart för nod-URL](node-quickstart.md)
