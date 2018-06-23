---
title: Python Snabbstart för Microsoft kognitiva Services, projekt svaret Sök | Microsoft Docs
description: Python exempel komma igång med projektet svaret sökning, kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9cb5406c616ed8e96d73c00c788a0d20f66dcabd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353931"
---
# <a name="project-answer-search-python-quickstart"></a>Projektet svaret Sök Python Snabbstart

I följande exempel Python skapar och skickar en begäran om information om ”Berg i Gibraltar”.

## <a name="prerequisites"></a>Förutsättningar

Hämta en åtkomstnyckel för den kostnadsfria utvärderingsversionen [kognitiva Services Labs](https://aka.ms/answersearchsubscription)

Det här exemplet används Python 3.6.4

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

````
## <a name="next-steps"></a>Nästa steg
- [C#-Snabbstart](c-sharp-quickstart.md)
- [Java-Snabbstart](java-quickstart.md)
- [Noden Snabbstart](node-quickstart.md)