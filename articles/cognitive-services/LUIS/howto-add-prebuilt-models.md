---
title: Färdiga modeller för Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS innehåller en uppsättning förbyggda modeller för att snabbt lägga till vanliga användar scenarier i konversationen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507785"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Lägg till fördefinierade modeller för vanliga användnings scenarier 

LUIS innehåller en uppsättning fördefinierade avsikter från de fördefinierade domänerna för att snabbt lägga till vanliga intentor och yttranden. Det här är ett snabbt och enkelt sätt att lägga till funktioner i din konversations klient app utan att behöva utforma modeller för dessa förmågor. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Lägga till en fördefinierad domän

1. På sidan **Mina appar** väljer du din app. Appen öppnas i avsnittet **build** i appen. 

1. På sidan **avsikter** väljer du **Lägg till fördefinierade domäner** i det nedre vänstra verktygsfältet. 

1. Välj **kalender** avsikten och välj sedan knappen **Lägg till domän** .

    ![Lägg till kalender fördefinierad domän](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Välj **avsikter** i den vänstra navigeringen för att Visa kalender avsikter. Varje avsikt från den här domänen har prefixet `Calendar.`. Tillsammans med yttranden läggs två entiteter för den här domänen till i appen: `Calendar.Location` och `Calendar.Subject`. 

### <a name="train-and-publish"></a>träna och publicera

1. När domänen har lagts till tränar du appen genom att välja **träna** i det övre högra verktygsfältet. 

1. I det översta verktygsfältet väljer du **publicera**. Publicera till **produktion**. 

1. När meddelandet grönt lyckades visas väljer du länken **Se listan över slut punkter** för att se slut punkterna.

1. Välj en slut punkt. En ny flik i webbläsaren öppnas med den slut punkten. Se till att fliken webbläsare är öppen och fortsätt till **test** avsnittet.

### <a name="test"></a>Testa

Testa den nya avsikten vid slut punkten genom att lägga till ett värde för parametern **q** : `Schedule a meeting with John Smith in Seattle next week`.

LUIS returnerar rätt avsikts-och mötes ämne:

```json
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

## <a name="add-a-prebuilt-intent"></a>Lägg till en fördefinierad avsikt

1. På sidan **Mina appar** väljer du din app. Appen öppnas i avsnittet **build** i appen. 

1. På sidan **avsikter** väljer du **Lägg till fördefinierat avsikt** från verktygsfältet ovanför listan med intenter. 

1. Välj **verktyg. Avbryt** avsikt från popup-dialogrutan. 

    ![Lägg till fördefinierad avsikt](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Klicka på knappen **Slutför** .

### <a name="train-and-test"></a>Träna och testa

1. När avsikten har lagts till tränar du appen genom att välja **träna** i det övre högra verktygsfältet. 

1. Testa den nya avsikten genom att välja **testa** i det högra verktygsfältet. 

1. I text rutan anger du yttranden för att avbryta:

    |Testa uttryck|Förutsägelseresultat|
    |--|:--|
    |Jag vill avbryta min flygning.|0,67|
    |Avbryt köpet.|0,52|
    |Avboka mötet.|0,56|

    ![Test-fördefinierat avsikt](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Lägg till en fördefinierad entitet

1. Öppna din app genom att klicka på namnet på sidan **Mina appar** och klicka sedan på **entiteter** på den vänstra sidan. 

1. På sidan **entiteter** klickar du på **Lägg till fördefinierad entitet**.

1. I dialog rutan **Lägg till fördefinierade entiteter** väljer du den datetimeV2 förskapade entiteten. 

    ![Dialog rutan Lägg till fördefinierad entitet](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Välj **Done** (Klar). När entiteten har lagts till behöver du inte träna appen. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicera för att Visa fördefinierad modell från förutsägelse slut punkt

Det enklaste sättet att visa värdet för en fördefinierad modell är att fråga från den publicerade slut punkten. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Markera entiteter som innehåller en fördefinierad Entity-token
 Om du har text, till exempel `HH-1234`, som du vill markera som en anpassad entitet _och_ du har ett [fördefinierat nummer](luis-reference-prebuilt-number.md) som läggs till i modellen, kan du inte markera den anpassade entiteten i Luis-portalen. Du kan markera den med API: et. 

 För att markera den här typen av token, där en del av den redan har marker ATS med en fördefinierad entitet, tar du bort den fördefinierade entiteten från LUIS-appen. Du behöver inte träna appen. Markera sedan token med din egen anpassade entitet. Lägg sedan till den färdiga entiteten i LUIS-appen igen.

 För ett annat exempel bör du överväga uttryck som en lista över klass inställningar: `I want first year spanish, second year calculus, and fourth year english lit.` om LUIS-appen har lagts till för att bygga ordnings tal måste `first`, `second`och `fourth` redan ha marker ATS med ordnings tal. Om du vill avbilda ordnings talet och-klassen kan du skapa en sammansatt entitet och omsluta den runt det förinställda ordnings talet och den anpassade entiteten för klass namn.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Bygg en modell från. csv med REST API: er](./luis-tutorial-node-import-utterances-csv.md)
