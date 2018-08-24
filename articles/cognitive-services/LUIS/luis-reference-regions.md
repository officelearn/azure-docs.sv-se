---
title: Språk Språkförståelse (LUIS) regioner och slutpunkter - Azure - Cognitive Service LUIS | Microsoft Docs
titleSuffix: Azure
description: Den region där du publicerar appen LUIS motsvarar den region eller plats som du anger i Azure-portalen när du skapar en Azure-LUIS slutpunktsnyckeln. När du publicerar en app genererar LUIS automatiskt en slutpunkts-URL för den region som är kopplat till nyckeln. Om du vill publicera en LUIS-app till flera regioner, behöver du minst en nyckel per region.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 7812ce37cb22c8774c785f5f645b8fef90b02a3e
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42815511"
---
# <a name="regions-and-keys"></a>Regioner och nycklar

Den region där du publicerar appen LUIS motsvarar den region eller plats som du anger i Azure-portalen när du skapar en Azure-LUIS slutpunktsnyckeln. När du [publicera en app](./luis-how-to-publish-app.md), LUIS genererar automatiskt en slutpunkts-URL för den region som är kopplat till nyckeln. Om du vill publicera en LUIS-app till flera regioner, behöver du minst en nyckel per region. 

## <a name="luis-website"></a>LUIS-webbplats
Det finns tre LUIS-webbplatser, baserat på region. Du måste skapa och publicera i samma region. 

|LUIS|Region|
|--|--|
|[www.Luis.AI][www.luis.ai]|Amerikansk<br>inte Europa<br>inte Australien|
|[AU.Luis.AI][au.luis.ai]|Australien|
|[EU.Luis.AI][eu.luis.ai]|Europa|


## <a name="publishing-regions"></a>Publicera regioner

LUIS-appar som skapats på https://www.luis.ai kan publiceras till alla slutpunkter utom den [Europeiska](#publishing-to-europe) och [Australien](#publishing-to-australia) regioner. 

Appen redigering region kan bara publiceras till en motsvarande publicera region. Om din app är för närvarande i fel region för redigering, exportera en app och importera den till rätt redigering regionen för din publishing region. 

 Global region | Redigera region | Publicera & fråga region   |   LUIS-webbplats | Slutpunkten URL-format   |
|-----|------|------|------|------|
| Asien | Västra USA| Östasien     | [www.Luis.AI][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | Västra USA| Sydostasien     | [www.Luis.AI][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Australien](#publishing-to-australia) | Australien, Öst| Australien, Öst     |   [AU.Luis.AI][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europa](#publishing-to-europe)| Västeuropa| Nordeuropa     | [EU.Luis.AI][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | Västeuropa| Västeuropa     | [EU.Luis.AI][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | Västra USA | Östra USA      |[www.Luis.AI][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | USA, östra 2     | [www.Luis.AI][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | USA, Central syd     | [www.Luis.AI][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | Västra USA | Västra centrala USA     |[www.Luis.AI][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | Västra USA |  [www.Luis.AI][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Nordamerika | Västra USA | Västra USA 2    | [www.Luis.AI][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Sydamerika | Västra USA | Brasilien, Syd     | [www.Luis.AI][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publicering till Europa

För att publicera till de europeiska regionerna måste du skapa LUIS appar i https://eu.luis.ai endast. Om du försöker publicera var som helst genom att använda en nyckel i regionen Europa, visas ett varningsmeddelande i LUIS. Använd i stället https://eu.luis.ai. LUIS-appar som skapats på [ https://eu.luis.ai ] [ eu.luis.ai] migreras inte automatiskt till andra regioner. Exportera och importera sedan LUIS-app för att migrera den.

## <a name="publishing-to-australia"></a>Publicering till Australien

För att publicera till australiensiska regioner måste du skapa LUIS appar på https://au.luis.ai endast. Om du försöker publicera var som helst genom att använda en nyckel i regionen australiensiska visas LUIS ett varningsmeddelande. Använd i stället https://au.luis.ai. LUIS-appar som skapats på [ https://au.luis.ai ] [ au.luis.ai] migreras inte automatiskt till andra regioner. Exportera och importera sedan LUIS-app för att migrera den.

## <a name="endpoints"></a>Slutpunkter

LUIS har för närvarande 2 slutpunkter: en för redigering och en för textanalys.

|Syfte|URL|
|--|--|
|Redigering|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Textanalys (fråga förutsagda)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

I följande tabell beskrivs de parametrar som är markerad med klammerparenteser `{}`, i föregående tabell.

|Parameter|Syfte|
|--|--|
|region|Azure-region - redigering och publicera har olika regioner|
|appID|LUIS-app-ID används i URL: en väg och finns på app-instrumentpanel|
|frågor och|uttryck-texten som skickas från klientprogram, till exempel chattrobot|


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Fördefinierade entitetsreferenser](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai