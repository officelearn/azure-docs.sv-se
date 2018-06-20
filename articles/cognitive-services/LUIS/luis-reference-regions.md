---
title: Språk förstå (THOMAS) regioner | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller en lista över THOMAS regioner för THOMAS webbplats, Azure-prenumerationer och regioner.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 86a20770178707f72cf2991ca08b6b98eaeaf0cf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36237290"
---
# <a name="regions-and-keys"></a>Regioner och nycklar

Den region där du publicerar appen THOMAS motsvarar den region eller plats som du anger i Azure-portalen när du skapar en nyckel för Azure THOMAS slutpunkt. När du [publicera en app](./PublishApp.md), THOMAS genererar automatiskt en slutpunkts-URL för den region som är associerade med nyckeln. Om du vill publicera en app THOMAS till flera regioner, behöver du minst en nyckeln per region. 

## <a name="luis-website"></a>THOMAS webbplats
Det finns tre THOMAS webbplatser, baserat på region. Du måste skapa och publicera i samma region. 

|LUIS|Region|
|--|--|
|[www.Luis.AI][www.luis.ai]|Amerikansk<br>inte Europa<br>inte Australien|
|[AU.Luis.AI][au.luis.ai]|Australien|
|[EU.Luis.AI][eu.luis.ai]|Europa|


## <a name="publishing-regions"></a>Publishing regioner

THOMAS appar som har skapats på https://www.luis.ai kan publiceras till alla slutpunkter utom den [Europeiska](#publishing-to-europe) och [australiska](#publishing-to-australia) regioner. 

Redigering region appen kan bara publiceras till en motsvarande publicera region. Om din app för närvarande är i fel redigering region, exportera appen och importera den till rätt redigering regionen för din publishing region. 

 Globala region | Redigering region | Publicera & frågar region   |   THOMAS webbplats | URL-format för slutpunkt   |
|-----|------|------|------|------|
| Asien | Västra USA| Östasien     | [www.Luis.AI][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | Västra USA| Sydostasien     | [www.Luis.AI][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Australien](#publishing-to-australia) | Östra Australien| Östra Australien     |   [AU.Luis.AI][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europa](#publishing-to-europe)| Västra Europa| Norra Europa     | [EU.Luis.AI][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | Västra Europa| Västra Europa     | [EU.Luis.AI][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | Västra USA | Östra USA      |[www.Luis.AI][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | Östra USA 2     | [www.Luis.AI][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | Södra centrala USA     | [www.Luis.AI][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | Västra USA | Västra centrala USA     |[www.Luis.AI][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | Västra USA | Västra USA |  [www.Luis.AI][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Nordamerika | Västra USA | Västra USA 2    | [www.Luis.AI][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Sydamerika | Västra USA | Södra Brasilien     | [www.Luis.AI][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publicering till Europa

Om du vill publicera till Europeiska områden måste du skapa THOMAS apparna på https://eu.luis.ai endast. Om du försöker publicera var som helst genom att använda en nyckel i Europa region visas THOMAS ett varningsmeddelande. Använd i stället https://eu.luis.ai. THOMAS appar som har skapats på [ https://eu.luis.ai ] [ eu.luis.ai] inte migrerar automatiskt till andra regioner. Exportera och importera sedan THOMAS appen för att migrera den.

## <a name="publishing-to-australia"></a>Publicering till Australien

För att publicera till australisk regioner måste du skapa THOMAS apparna på https://au.luis.ai endast. Om du försöker publicera var som helst genom att använda en nyckel i australisk region visas THOMAS ett varningsmeddelande. Använd i stället https://au.luis.ai. THOMAS appar som har skapats på [ https://au.luis.ai ] [ au.luis.ai] inte migrerar automatiskt till andra regioner. Exportera och importera sedan THOMAS appen för att migrera den.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för färdiga entiteter](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai