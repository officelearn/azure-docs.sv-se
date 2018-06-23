---
title: Python Snabbstart för Project URL Preview - kognitiva Microsoft-tjänster | Microsoft Docs
description: Skriptexempel att snabbt komma igång med projektet URL förhandsversionen i Microsoft kognitiva Services på Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 78b2d83b02aa9ea32509029c7456e04e420b8572
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354015"
---
# <a name="url-preview-python-quickstart"></a>URL: en förhandsgranskning Python-Snabbstart

I följande exempel för Python skapas en förhandsgranskning för URL: en för webbplatsen SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Förutsättningar

Hämta en åtkomstnyckel för den kostnadsfria utvärderingsversionen [kognitiva Services Labs](https://aka.ms/answersearchsubscription)

Det här exemplet använder Python 3,6.

## <a name="code-scenario"></a>Koden scenario 

Följande kod skapar en URL-förhandsgranskning.
De är implementerade i följande steg:
1. Deklarera variabler för att ange slutpunkten av värd och sökväg.
2. Ange fråge-URL för att förhandsgranska och Lägg till Frågeparametern.  
3. Ange Frågeparametern.
4. Definiera sökfunktionen som skapar begäran och lägger till den *Ocp-Apim-prenumeration-nyckeln* huvud.
5. Ange den *Ocp-Apim-prenumeration-nyckeln* huvud. 
6. Kontrollera anslutningen och skicka begäran.
7. Skriva ut JSON-resultaten.

Den fullständiga koden för den här demon följande:

````
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
````
## <a name="next-steps"></a>Nästa steg
- [C#-Snabbstart](csharp.md)
- [Java-Snabbstart](java-quickstart.md)
- [JavaScript-Snabbstart](javascript.md)
- [Snabbstart för nod-URL](node-quickstart.md)