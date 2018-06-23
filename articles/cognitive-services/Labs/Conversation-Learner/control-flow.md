---
title: Kontrollflöde för konversationen deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Läs mer om Kontrollflöde konversation deltagaren.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 592ca82db7e0ab3ff89d25b61f38f8b226f3bc86
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353934"
---
## <a name="control-flow"></a>Kontrollflöde

Det här dokumentet beskriver Kontrollflöde i konversationen deltagaren (CL) som visas i den under diagrammet.

![](media/controlflow.PNG)

1. Användaren anger ett ord eller en fras i bot, till exempel ”vad är väder i Seattle”?
1. CL skickar användardata till en maskininlärningsmodell som extraherar entiteter
    - Den här modellen är att bygga av konversationen deltagaren och hos www.luis.ai
2. Någon extraheras entiteter och användaren input text, skickas till entiteten identifiering Återanropsmetoden i den bot kod.
    - Den här koden kan ange/Rensa/ändra entitet värden
1. CL neurala nätverket tar sedan utdata entitet extrahering indata från användaren, och alla åtgärder som definierats i bot-resultat
    - I det här exemplet är åtgärden högsta sannolikheten att tillhandahålla väder prognosen:

![](media/controlflow_forecast.PNG)

5. Den valda åtgärden i det här fallet kräver ett API-anrop för att hämta väder prognos. 
6. Detta API har registrerats med hjälp av CL. AddAPICallback-metoden anropas sedan.  Resultatet av denna API returneras till användaren som ett meddelande, till exempel Sunny med en hög 67.
7. Anropet görs sedan att det neurala nätverket för att avgöra nästa åtgärd baserat på det föregående steget.
8. Det neurala nätverket sedan beräknar den nästa uppsättningen möjliga åtgärder och den valda åtgärden visas för användaren i det här fallet 'Något annat ”?

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hur de ska lära med konversation deltagaren ](./how-to-teach-cl.md)
