---
title: Fördefinierade entiteter i THOMAS | Microsoft Docs
description: Den här artikeln innehåller en lista över fördefinierade entiteter som ingår i språk förstå Intelligent tjänster (THOMAS).
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: cahann
ms.reviewer: v-geberr
ms.openlocfilehash: 4858de8b8517016ca385e9105ca8948b66aa683b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36322053"
---
# <a name="prebuilt-entities"></a>Fördefinierade entiteter

THOMAS innehåller en uppsättning fördefinierade entiteter för att identifiera vanliga typer av information som datum, tid, siffror, mått och valuta. Fördefinierade entitet stöd varierar beroende på appen THOMAS kultur. En fullständig lista över de fördefinierade entiteter som THOMAS stöder, inklusive stöd av kultur, finns det [färdiga Entitetsreferens](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **Builtin.datetime** är föråldrad. Den har ersatts av [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), vilket ger igenkänning av datum och tidsintervall samt förbättrad igenkänning av tvetydiga datum och tid.

## <a name="add-a-prebuilt-entity"></a>Lägga till en fördefinierad entitet

1. Öppna din app genom att klicka på namnet på **Mina appar** , och klickar sedan på **entiteter** till vänster. 
2. På den **entiteter** klickar du på **hantera färdiga entiteter**.

    ![Entiteter sida - hantera färdiga entiteter](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. I **lägga till fördefinierade enheter** dialogrutan klickar du på den fördefinierade enheten som du vill lägga till (till exempel ”datetimeV2”). Klicka sedan på **Spara**.

    ![Fördefinierade entitet dialogrutan Lägg till](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Använda en fördefinierad nummer entitet
När en fördefinierad entitet ingår i ditt program, med dess förutsägelser i ditt publicerade program. Fördefinierade entiteternas beteende är före utbildade och **kan** ändras. Följ dessa steg om du vill se hur en fördefinierad entitet fungerar:

1. Lägg till en **nummer** enhet till din app sedan [Train](interactive-test.md) och [publicera](PublishApp.md) appen.
2. Klicka på slutpunkts-URL i den **publicera appen** kan du öppna THOMAS slutpunkten i en webbläsare. 
3. Lägg till en utterance till den URL som innehåller ett numeriskt uttryck. Du kan till exempel skriva i `buy two plane ticktets`, och se som identifierar THOMAS `two` som en `builtin.number` entiteten och identifierar `2` som värdet på den `resolution` fältet. Den `resolution` fältet hjälper dig att motsvara en kanonisk form som är enklare för ditt klientprogram att använda tal och datum. 

    ![utterance i webbläsare som innehåller ett antal entitet](./media/luis-use-prebuilt-entity/browser-query.png)

THOMAS Intelligent känner igen siffror som inte ingår i formulär som inte är standard. Prova att använda olika numeriska uttryck i din utterances och se THOMAS returnerar.

I följande exempel visas en JSON-svar från THOMAS, som innehåller värdet 24 för utterance ”24” upplösning.

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>Använda en fördefinierad datetimeV2 entitet
Den **datetimeV2** färdiga entitet identifierar datum, tider, datumintervall och tidsvaraktigheter. Följ dessa steg för att se hur `datetimeV2` färdiga entiteten fungerar:

1. Lägg till en **datetimeV2** enhet till din app sedan [Train](interactive-test.md) och [publicera](PublishApp.md) appen.
2. Klicka på slutpunkts-URL i den **publicera appen** kan du öppna THOMAS slutpunkten i en webbläsare. 
3. Lägg till en utterance till den URL som innehåller ett datumintervall. Du kan till exempel skriva i `book a flight tomorrow`, och se som identifierar THOMAS `tomorrow` som en `builtin.datetimeV2.date` entiteten och identifierar framtidens datum som värdet på den `resolution` fältet. 

I följande exempel visas hur JSON-svar från THOMAS kan se ut om dagens datum oktober 31 2017.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Fördefinierade enhetsreferens](./luis-reference-prebuilt-entities.md)
