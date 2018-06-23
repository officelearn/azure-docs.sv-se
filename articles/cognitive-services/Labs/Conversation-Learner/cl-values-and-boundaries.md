---
title: Standardkonfigurationen för konversationen deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Läs mer om standardkonfigurationen för konversationen deltagaren.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 56e2140b83bf1c5722a459c14f31b2b4b0ba6b15
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353949"
---
# <a name="default-values-and-boundaries"></a>Standardvärden och gränser

Det här dokumentet beskriver standardkonfigurationen för konversationen deltagaren och nyckel gränser.

## <a name="default-configuration"></a>Standardkonfigurationen

Parameter | Standardvärde
--- | --- 
Standard Sessionstidsgräns | 30 minuter

## <a name="boundaries"></a>Gränser

Parameter | Gräns
--- | --- 
Redigera API max HTTP-anrop per månad | 5M
Redigera API max HTTP-anrop per sekund | 25
Sessionen API, max HTTP-anrop per månad | 500 000
Sessionen API, max HTTP-anrop per sekund | 10
Max antal anpassade (icke-programmässiga) enheter per program | Se [THOMAS gränser doc](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries); i praktiken faktiska antalet kan vara något mindre
Max antal förskapad entiteter per program | Se [THOMAS gränser doc](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)
Högsta antal enheter (i totalt) per program | 100
Max antal åtgärder per program | 32
Max antal train dialogrutor per program | 1000
Högsta antal användare aktiverar per tåg dialogrutan | 100
Max antal loggen dialogrutor per program | Inga förinställda gränsen, men loggen dialogrutor behålls endast för en bestämd tid innan det tas bort.  Dessutom visar konversation deltagaren Användargränssnittet 100 loggen dialogrutor i taget. 
Max antal program per användare | Ingen förinställda gräns
Max antal sekventiella-wait-åtgärder | 5 (*)

(*) Alla åtgärder för icke-wait maskeras efter 5 sekventiella-wait-åtgärder och konversation deltagaren väljer bland tillgängliga vänta åtgärder.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med konversation deltagaren](./quickstart.md)
