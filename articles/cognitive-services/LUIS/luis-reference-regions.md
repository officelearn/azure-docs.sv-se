---
title: Publicerings regioner &-slutpunkter – LUIS
titleSuffix: Azure Cognitive Services
description: Den region som anges i Azure Portal är samma där du kommer att publicera LUIS-appen och en slut punkts-URL genereras för samma region.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: fe4436afe56e631ae308b70ae35b1fb4a7c32e11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669192"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Redigerings-och publicerings regioner och tillhör ande nycklar

Tre redigerings regioner stöds av motsvarande LUIS-portaler. Om du vill publicera en LUIS-app i fler än en region behöver du minst en nyckel per region. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS redigerings regioner
Det finns tre LUIS redigerings portaler, baserat på region. Du måste redigera och publicera i samma region. 

|LUIS|Redigerings region|Namn på Azure-region|
|--|--|--|
|[www.luis.ai][www.luis.ai]|USA:s<br>inte Europa<br>inte Australien| `westus`|
|[au.luis.ai][au.luis.ai]|Australien| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Europa|`westeurope`|

Redigerings regioner har [parat på växlings bara regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicera regioner och Azure-resurser
Appen publiceras till alla regioner som är kopplade till de LUIS-resurser som lagts till i LUIS-portalen. För en app som skapas på [www.Luis.AI][www.luis.ai], om du till exempel skapar en Luis-eller kognitiv tjänst resurs i **väst** och [lägger till den i appen som en resurs](luis-how-to-azure-subscription.md), publiceras appen i den regionen. 

## <a name="public-apps"></a>Offentliga appar
En offentlig app publiceras i alla regioner så att en användare med en regions-baserad LUIS resurs nyckel kan komma åt appen i den region som är kopplad till deras resurs nyckel.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publicerings regioner är knutna till redigerings regioner

Det går bara att publicera appen för redigerings region i en motsvarande publicerings region. Om din app för närvarande är i fel redigerings region, exportera appen och importera den till rätt redigerings region för publicerings regionen. 

LUIS-appar som skapas på https://www.luis.ai kan publiceras till alla slut punkter förutom de [Europeiska](#publishing-to-europe) och [australiska](#publishing-to-australia) regionerna. 

## <a name="publishing-to-europe"></a>Publicera till Europa

Om du vill publicera till de europeiska regionerna skapar du LUIS-appar endast på https://eu.luis.ai. Om du försöker publicera någon annan stans med en nyckel i regionen Europa, visar LUIS ett varnings meddelande. Använd i stället https://eu.luis.ai. LUIS-appar som skapats på [https://eu.luis.ai][eu.luis.ai] migreras inte automatiskt till andra regioner. Exportera och importera LUIS-appen för att migrera den.

## <a name="europe-publishing-regions"></a>Publicerings regioner för Europa

 Global region | Redigera API-region & redigerings webbplats| Publicerar & fråge område<br>`API region name`   |  URL-format för slut punkt   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Frankrike, centrala<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Norra Europa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Västra Europa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Storbritannien, södra<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publicera till Australien

För att publicera till australiensiska regioner skapar du LUIS-appar endast på https://au.luis.ai. Om du försöker publicera någon annan stans med hjälp av en nyckel i regionen australisk, visar LUIS ett varnings meddelande. Använd i stället https://au.luis.ai. LUIS-appar som skapats på [https://au.luis.ai][au.luis.ai] migreras inte automatiskt till andra regioner. Exportera och importera LUIS-appen för att migrera den.

## <a name="australia-publishing-regions"></a>Australien, publicerings regioner

 Global region | Redigera API-region & redigerings webbplats| Publicerar & fråge område<br>`API region name`   |  URL-format för slut punkt   |
|-----|------|------|------|
| [Australien](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Östra Australien<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publicera till andra regioner

Om du vill publicera till andra regioner skapar du LUIS-appar endast på [https://www.luis.ai](https://www.luis.ai) . 

## <a name="other-publishing-regions"></a>Andra publicerings regioner

 Global region | Redigera API-region & redigerings webbplats| Publicerar & fråge område<br>`API region name`   |  URL-format för slut punkt   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Sydafrika, norra<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Indien, centrala<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Östasien<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Östra Japan<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Västra Japan<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Sydkorea, centrala<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Sydostasien<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Centrala Kanada<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Centrala USA<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Östra USA<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, östra 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Norra centrala USA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Södra centrala USA<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Västra centrala USA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Västra USA<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Västra USA 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Sydamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Södra Brasilien<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Slutpunkter

LUIS har för närvarande 2 slut punkter: en för redigering och en för analys av fråga förutsägelse.

|Syfte|URL|
|--|--|
|Redigering|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Text analys (fråga förutsägelse)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

I följande tabell beskrivs parametrarna, som anges med klammerparenteser `{}`, i föregående tabell.

|Parameter|Syfte|
|--|--|
|regionen|Azure region – redigering och publicering har olika regioner|
|Undanta|LUIS app-ID som används i URL-vägen och finns på app-instrumentpanelen|
|c|uttryck text som skickats från klient programmet, till exempel chatt-robot|

## <a name="failover-regions"></a>Redundans

Varje region har en sekundär region att redundansväxla till. Europa växlar över i Europa och Australien växlar över inom Australien.

Redigerings regioner har [parat på växlings bara regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för fördefinierade entiteter](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
