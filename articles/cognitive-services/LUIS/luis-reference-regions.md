---
title: Publicera regioner och slutpunkter
titleSuffix: Azure Cognitive Services
description: 3 redigering regioner och deras portaler stöd för många publishing regioner. Den region där du publicerar appen LUIS motsvarar den region eller plats som du anger i Azure-portalen när du skapar en Azure-LUIS slutpunktsnyckeln. När du publicerar en app genererar LUIS automatiskt en slutpunkts-URL för den region som är kopplat till nyckeln.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 20ea2eb632a6d685351178691cc3d0f58a567902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599271"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Skapa och publicera regioner och associerade nycklar

Tre redigering regioner som stöds av motsvarande LUIS-portaler. Om du vill publicera en LUIS-app till flera regioner, behöver du minst en nyckel per region. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS redigering regioner
Det finns tre LUIS redigering portaler, baserat på region. Du måste skapa och publicera i samma region. 

|LUIS|Redigera region|Namn på Azure-region|
|--|--|--|
|[www.Luis.AI][www.luis.ai]|Amerikansk<br>inte Europa<br>inte Australien| `westus`|
|[AU.Luis.AI][au.luis.ai]|Australien| `australiaeast`|
|[EU.Luis.AI][eu.luis.ai]|Europa|`westeurope`|

Redigering regioner har [länkade regioner för redundans](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicera regioner och Azure-resurser
Appen har publicerats i alla regioner som associeras med LUIS-resurser som har lagts till i LUIS-portalen. Till exempel för en app som skapats på [www.luis.ai][www.luis.ai], om du skapar en LUIS eller Cognitive Service resurs i **westus** och [lägga till den i appen som en resurs ](luis-how-to-azure-subscription.md), appen publiceras i den regionen. 

## <a name="public-apps"></a>Offentliga appar
En offentlig app publiceras i alla regioner så att en användare med en region-baserade LUIS Resursnyckeln har åtkomst till appen i regionen som är associerad med deras Resursnyckeln.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publicera regioner är knutna till redigering av regioner

Appen redigering region kan bara publiceras till en motsvarande publicera region. Om din app är för närvarande i fel region för redigering, exportera en app och importera den till rätt redigering regionen för din publishing region. 

LUIS-appar som skapats på https://www.luis.ai kan publiceras till alla slutpunkter utom den [Europeiska](#publishing-to-europe) och [Australien](#publishing-to-australia) regioner. 

## <a name="publishing-to-europe"></a>Publicering till Europa

För att publicera till de europeiska regionerna måste du skapa LUIS appar i https://eu.luis.ai endast. Om du försöker publicera var som helst genom att använda en nyckel i regionen Europa, visas ett varningsmeddelande i LUIS. Använd i stället https://eu.luis.ai. LUIS-appar som skapats på [ https://eu.luis.ai ] [ eu.luis.ai] migreras inte automatiskt till andra regioner. Exportera och importera sedan LUIS-app för att migrera den.

## <a name="europe-publishing-regions"></a>Publicera regioner i Europa

 Global region | Redigera API region & Redigera webbplats| Publicera & fråga region<br>`API region name`   |  Slutpunkten URL-format   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Frankrike, centrala<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Norra Europa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Västra Europa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Storbritannien, södra<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publicering till Australien

För att publicera till australiensiska regioner måste du skapa LUIS appar på https://au.luis.ai endast. Om du försöker publicera var som helst genom att använda en nyckel i regionen australiensiska visas LUIS ett varningsmeddelande. Använd i stället https://au.luis.ai. LUIS-appar som skapats på [ https://au.luis.ai ] [ au.luis.ai] migreras inte automatiskt till andra regioner. Exportera och importera sedan LUIS-app för att migrera den.

## <a name="australia-publishing-regions"></a>Publicera regionerna i Australien

 Global region | Redigera API region & Redigera webbplats| Publicera & fråga region<br>`API region name`   |  Slutpunkten URL-format   |
|-----|------|------|------|
| [Australien](#publishing-to-australia) | `australiaeast`<br>[AU.Luis.AI][au.luis.ai]| Östra Australien<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publicering till andra regioner

För att publicera till andra regioner måste du skapa LUIS appar i [ https://www.luis.ai ](https://www.luis.ai) endast. 

## <a name="other-publishing-regions"></a>Andra publishing regioner

 Global region | Redigera API region & Redigera webbplats| Publicera & fråga region<br>`API region name`   |  Slutpunkten URL-format   |
|-----|------|------|------|
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Indien, centrala<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Östasien<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Östra Japan<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Västra Japan<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Sydkorea, centrala<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.Luis.AI][www.luis.ai]| Sydostasien<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
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

## <a name="endpoints"></a>Slutpunkter

LUIS har för närvarande 2 slutpunkter: en för redigering och en för förutsägelse Frågeanalys.

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

## <a name="failover-regions"></a>Redundans regioner

Varje region har en sekundär region för att växla över till. Europa misslyckas over i Europa och Australien växlas över i Australien.

Redigering regioner har [länkade regioner för redundans](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Fördefinierade entitetsreferenser](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
