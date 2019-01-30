---
title: Kontrollflöde för konversationen Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Läs mer om Kontrollflöde Konversationsdeltagare.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07f4506b7dd0ac8ca0462e2a418983e561859c91
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208388"
---
## <a name="control-flow"></a>Kontrollflöde

Det här dokumentet beskriver Kontrollflöde för konversationen Learner (CL) som visas på den nedanstående diagram.

![](media/controlflow.PNG)

1. Användaren anger en term eller fras i bot, till exempel ”vad är vädret i Seattle”?
1. CL skickar indata från användaren till en maskininlärningsmodell som extraherar entiteter
    - Den här modellen är version av Konversationsdeltagare och värd www.luis.ai
1. Någon extraheras entiteter och användarens indata-text, skickas till entiteten identifiering motringningsmetoden i robotens kod.
    - Den här koden kan set/Rensa/ändra entitetsvärden
1. CL neuralt nätverk tar sedan utdata från entitetextrahering och indata från användaren och alla åtgärder som definierats i roboten-poäng
    - I det här exemplet är den högsta sannolikhet åtgärden att tillhandahålla väderprognosen:

    ![](media/controlflow_forecast.PNG)

1. Den valda åtgärden i det här fallet kräver ett API-anrop för att hämta väderprognosen. 
1. Detta API har registrerats med hjälp av CL. AddCallback-metoden anropas sedan.  Resultatet av detta API returneras sedan till användaren som ett meddelande, till exempel Sunny med en hög 67.
1. Anropet görs sedan till neurala nätverk för att avgöra nästa åtgärd baserat på föregående steg.
1. Det neurala nätverket beräknar sedan nästa uppsättning möjliga åtgärder och den valda åtgärden presenteras för användaren i det här fallet ”allt annat”?

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hur de ska lära med Konversationsdeltagare ](./how-to-teach-cl.md)
