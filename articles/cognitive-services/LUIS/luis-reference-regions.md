---
title: Publicera regioner och slutpunkter
titleSuffix: Azure Cognitive Services
description: Den region där du publicerar appen LUIS motsvarar den region eller plats som du anger i Azure-portalen när du skapar en Azure-LUIS slutpunktsnyckeln. När du publicerar en app genererar LUIS automatiskt en slutpunkts-URL för den region som är kopplat till nyckeln.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/10/2019
ms.author: diberry
ms.openlocfilehash: 093c60287b16073a6c04e824dc439f5dc069ec96
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863677"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Skapa och publicera regioner och associerade nycklar

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

 Global region | Redigera API region & Redigera webbplats| Publicera & fråga region<br>`API region name`   |  Slutpunkten URL-format   |
|-----|------|------|------|
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Indien, centrala<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Östasien<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Östra Japan<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Västra Japan<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Sydkorea, centrala<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Sydostasien<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Australien](#publishing-to-australia) | `australiaeast`<br>[AU.Luis.AI][au.luis.ai]| Östra Australien<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europa](#publishing-to-europe)| `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Frankrike, centrala<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe)| `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Norra Europa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Västra Europa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Storbritannien, södra<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika |`westus`<br>[www.Luis.AI][www.luis.ai] | Centrala Kanada<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika |`westus`<br>[www.Luis.AI][www.luis.ai] | Centrala USA<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika |`westus`<br>[www.Luis.AI][www.luis.ai] | Östra USA<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.Luis.AI][www.luis.ai] | USA, östra 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.Luis.AI][www.luis.ai] | Norra centrala USA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | `westus`<br>[www.Luis.AI][www.luis.ai] | Södra centrala USA<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika |`westus`<br>[www.Luis.AI][www.luis.ai] | Västra centrala USA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.Luis.AI][www.luis.ai] | Västra USA<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Nordamerika |`westus`<br>[www.Luis.AI][www.luis.ai] | Västra USA 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Sydamerika | `westus`<br>[www.Luis.AI][www.luis.ai] | Södra Brasilien<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |




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
