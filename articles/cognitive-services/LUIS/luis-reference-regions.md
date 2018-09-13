---
title: Publicering regioner och slutpunkter – LUIS
titleSuffix: Azure Cognitive Services
description: Den region där du publicerar appen LUIS motsvarar den region eller plats som du anger i Azure-portalen när du skapar en Azure-LUIS slutpunktsnyckeln. När du publicerar en app genererar LUIS automatiskt en slutpunkts-URL för den region som är kopplat till nyckeln. Om du vill publicera en LUIS-app till flera regioner, behöver du minst en nyckel per region.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/11/2018
ms.author: diberry
ms.openlocfilehash: 48e1e19d2d425fe123e5a0c369ecebf623b74eb2
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721326"
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

## <a name="regions-and-azure-resources"></a>Regioner och Azure-resurser
Appen har publicerats i alla regioner som associeras med LUIS-resurser som har lagts till i LUIS-portalen. Till exempel för en app som skapats på [www.luis.ai][www.luis.ai], om du skapar en LUIS-resurs i **westus** och lägga till den i appen som en resurs, appen publiceras i den regionen. 

## <a name="public-apps"></a>Offentliga appar
En offentlig app publiceras i alla regioner så att en användare med en region-baserade LUIS Resursnyckeln har åtkomst till appen i regionen som är associerad med deras Resursnyckeln.

## <a name="publishing-regions"></a>Publicera regioner

LUIS-appar som skapats på https://www.luis.ai kan publiceras till alla slutpunkter utom den [Europeiska](#publishing-to-europe) och [Australien](#publishing-to-australia) regioner. 

Appen redigering region kan bara publiceras till en motsvarande publicera region. Om din app är för närvarande i fel region för redigering, exportera en app och importera den till rätt redigering regionen för din publishing region. 

 Global region | Redigera region | Publicera & fråga region   |   LUIS-webbplats | Slutpunkten URL-format   |
|-----|------|------|------|------|
| Asien | Västra USA| Östasien     | [www.Luis.AI][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | Västra USA| Sydostasien     | [www.Luis.AI][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Australien](#publishing-to-australia) | Östra Australien| Östra Australien     |   [AU.Luis.AI][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europa](#publishing-to-europe)| Västra Europa| Norra Europa     | [EU.Luis.AI][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | Västra Europa| Västra Europa     | [EU.Luis.AI][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | Västra USA | Östra USA      |[www.Luis.AI][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | USA, östra 2     | [www.Luis.AI][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | Södra centrala USA     | [www.Luis.AI][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | Västra USA | Västra centrala USA     |[www.Luis.AI][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | Västra USA |  [www.Luis.AI][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Nordamerika | Västra USA | Västra USA 2    | [www.Luis.AI][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Sydamerika | Västra USA | Södra Brasilien     | [www.Luis.AI][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

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