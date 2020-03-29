---
title: Publicera regioner & slutpunkter - LUIS
description: Den region som anges i Azure-portalen är densamma där du publicerar LUIS-appen och en slutpunkts-URL genereras för samma region.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292089"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Skapa och publicera regioner och tillhörande nycklar

Tre redigeringsområden stöds av motsvarande LUIS-portaler. Om du vill publicera en LUIS-app i mer än en region behöver du minst en nyckel per region.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS-redigeringsområden
Det finns tre LUIS-redigeringsportaler, baserade på region. Du måste skapa och publicera i samma region.

|LUIS|Redigeringsregion|Namn på Azure-region|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|USA<br>inte Europa<br>inte Australien| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Australien| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

Redigeringsregioner har [parkopplat överväxlingsregioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicera regioner och Azure-resurser
Appen publiceras i alla regioner som är associerade med LUIS-resurserna som lagts till i LUIS-portalen. Till exempel för en app som skapats på [www.luis.ai][www.luis.ai], om du skapar en LUIS- eller Cognitive Service-resurs i **westus** och [lägger till den](luis-how-to-azure-subscription.md)i appen som en resurs , publiceras appen i det området.

## <a name="public-apps"></a>Offentliga appar
En offentlig app publiceras i alla regioner så att en användare med en regionbaserad LUIS-resursnyckel kan komma åt appen i vilken region som är associerad med deras resursnyckel.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publiceringsregioner är knutna till redigeringsregioner

Appen för redigeringsregionen kan bara publiceras i en motsvarande publiceringsregion. Om din app för närvarande är i fel redigeringsregion exporterar du appen och importerar den till rätt redigeringsregion för publiceringsregionen.

LUIS-appar https://www.luis.ai som skapats på kan publiceras på alla slutpunkter utom [de europeiska](#publishing-to-europe) och [australiska](#publishing-to-australia) regionerna.

## <a name="publishing-to-europe"></a>Publicering till Europa

Om du bara vill publicera till https://eu.luis.ai de europeiska regionerna skapar du LUIS-appar. Om du försöker publicera någon annanstans med hjälp av en nyckel i Europa-regionen visas ett varningsmeddelande i LUIS. Använd i https://eu.luis.aistället . LUIS-appar [https://eu.luis.ai][eu.luis.ai] som skapas vid migrerar inte automatiskt till andra regioner. Exportera och importera sedan LUIS-appen för att migrera den.

## <a name="europe-publishing-regions"></a>Europa förlagsregioner

 Global region | Skapa API-region & författarwebbplats| Publicera & frågeområde<br>`API region name`   |  URL-format för slutpunkt   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Frankrike, centrala<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa, norra<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa, västra<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Storbritannien, södra<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publicering till Australien

Om du bara vill publicera till https://au.luis.ai de australiska regionerna skapar du LUIS-appar. Om du försöker publicera någon annanstans med hjälp av en nyckel i den australiska regionen visas ett varningsmeddelande för LUIS. Använd i https://au.luis.aistället . LUIS-appar [https://au.luis.ai][au.luis.ai] som skapas vid migrerar inte automatiskt till andra regioner. Exportera och importera sedan LUIS-appen för att migrera den.

## <a name="australia-publishing-regions"></a>Australien förlagsregioner

 Global region | Skapa API-region & författarwebbplats| Publicera & frågeområde<br>`API region name`   |  URL-format för slutpunkt   |
|-----|------|------|------|
| [Australien](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australien, östra<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publicering till andra regioner

Om du bara vill publicera till [https://www.luis.ai](https://www.luis.ai) de andra regionerna skapar du LUIS-appar.

## <a name="other-publishing-regions"></a>Andra publiceringsregioner

 Global region | Skapa API-region & författarwebbplats| Publicera & frågeområde<br>`API region name`   |  URL-format för slutpunkt   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Sydafrika North<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Indien, centrala<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Asien, östra<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, östra<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, västra<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Sydkorea, centrala<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Sydostasien<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada, centrala<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, centrala<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, östra<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, östra 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, norra centrala<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, södra centrala<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, västra centrala<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, västra<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, västra 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Sydamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brasilien, södra<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Slutpunkter

Läs mer om [slutpunkterna för redigering och förutsägelse](developer-reference-resource.md).

## <a name="failover-regions"></a>Redundansregioner

Varje region har en sekundär region att växla över till. Europa misslyckas över i Europa och Australien misslyckas över inne i Australien.

Redigeringsregioner har [parkopplat överväxlingsregioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för fördefinierade enheter](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
