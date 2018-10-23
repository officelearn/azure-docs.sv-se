---
title: Fördefinierade domäner för Språkförståelse (LUIS)
titleSuffix: Azure Cognitive Services
description: LUIS innehåller en uppsättning fördefinierade domäner för att snabbt lägga till vanliga, konversationsanpassade användarscenarier.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: b1c7ced4a934ea5d094e0c54a295870986f09933
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651896"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Lägg till fördefinierade domäner för vanliga Användningsscenarier 

LUIS innehåller en uppsättning fördefinierade avsikter från fördefinierade domäner för att snabbt lägga till vanliga avsikter och yttranden. Det här är ett snabbt och enkelt sätt att lägga till funktioner till konversationsanpassade klientappen utan att behöva utforma modeller för dessa funktioner. 

## <a name="add-a-prebuilt-domain"></a>Lägga till en fördefinierad domän

1. På den **Mina appar** väljer du din app. Din app öppnas den **skapa** i appen. 

1. På den **avsikter** väljer **Lägg till fördefinierade domäner** kvar från längst ned i verktygsfältet. 

1. Välj den **kalender** avsiktlig och väljer sedan **Lägg till domän** knappen.

    ![Lägg till kalender fördefinierade domän](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Välj **avsikter** i det vänstra navigeringsfönstret visa kalendern avsikter. Varje avsikt från den här domänen har prefixet `Calendar.`. Tillsammans med yttranden, två entiteter för den här domänen har lagts till i appen: `Calendar.Location` och `Calendar.Subject`. 

## <a name="train-and-publish"></a>träna och publicera

1. När domänen har lagts till, träna appen genom att välja **träna** i övre högra verktygsfältet. 

1. I det översta verktygsfältet väljer **publicera**. Publicera till **produktion**. 

1. När meddelandet grönt visas väljer du den **finns i listan över slutpunkter** länken för att se slutpunkterna.

1. Välj en slutpunkt. En ny webbläsarflik öppnas till denna slutpunkt. Håll webbläsarfliken öppen och Fortsätt den **Test** avsnittet.

## <a name="test"></a>Testa

Testa nya avsikten vid slutpunkten av lagt till ett värde för den **q** parameter: `Schedule a meeting with John Smith in Seattle next week`.

LUIS returnerar rätt avsikt och Mötets ämne:

```JSON
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Referens för fördefinierade](./luis-reference-prebuilt-domains.md)
