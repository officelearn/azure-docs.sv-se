---
title: Conversation Learner kontroll flöde – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om Conversation Learner kontroll flöde.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec839c1a930ffbe73989149360f1b02866a3c50
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705313"
---
## <a name="control-flow"></a>Kontrollflöde

Det här dokumentet beskriver kontroll flödet för Conversation Learner (CL) som visas i diagrammet nedan.

![](media/controlflow.PNG)

1. Användaren anger en term eller fras i bot, till exempel "Vad är väder i Seattle?"
1. CL överför användarindata till en maskin inlärnings modell som extraherar entiteter
   - Den här modellen bygger på Conversation Learner och hanteras av www.luis.ai
1. Alla extraherade entiteter och användarens indata-text skickas till återkallnings metoden för entitets identifiering i robotens kod.
    - Den här koden kan ställa in/rensa/ändra enhets värden
1. CL neurala-nätverk tar utdata från enhets extraheringen och användarindata, och visar alla åtgärder som definierats i bot
   - I det här exemplet är den högsta sannolikhets åtgärden att tillhandahålla väder prognosen:

     ![](media/controlflow_forecast.PNG)

1. Den valda åtgärden, i det här fallet, kräver ett API-anrop för att hämta väder prognosen. 
1. Detta API, som har registrerats med hjälp av CL. AddCallback-metoden anropas sedan.  Resultatet av detta API returneras sedan till användaren som ett meddelande, till exempel solig med en hög av 67.
1. Anropet görs sedan till neurala-nätverket för att fastställa nästa åtgärd baserat på föregående steg.
1. Neurala-nätverket förutsäger sedan nästa uppsättning möjliga åtgärder och den valda åtgärden presenteras för användaren, i det här fallet "allt annat?"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att lära med Conversation Learner](./how-to-teach-cl.md)
