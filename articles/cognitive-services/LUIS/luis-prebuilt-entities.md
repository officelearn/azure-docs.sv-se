---
title: Färdiga entiteter i LUIS | Microsoft Docs
description: Den här artikeln innehåller en lista över de fördefinierade entiteter som ingår i Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: cahann
ms.reviewer: v-geberr
ms.openlocfilehash: f7abf6d8a9f0fe18017fe5c54801ac0d3b6c379e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170791"
---
# <a name="prebuilt-entities"></a>Fördefinierade entiteter

LUIS innehåller en uppsättning fördefinierade entiteter för att identifiera vanliga typer av information som datum, tider, nummer, mätningar och valuta. Fördefinierade entitet support varierar beroende på kultur LUIS-appen. En fullständig lista över de fördefinierade entiteter som LUIS stöder, bland annat stöd av kulturen, finns i den [fördefinierade Entitetsreferens](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **Builtin.datetime** är inaktuell. Ersätter [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), som tillhandahåller igenkänning av datum- och tidsintervall, samt bättre tolkning av tvetydiga datum och tider.

## <a name="add-a-prebuilt-entity"></a>Lägg till en fördefinierade entitet

1. Öppna din app genom att klicka på namnet på **Mina appar** , och klicka sedan på **entiteter** till vänster. 
2. På den **entiteter** klickar du på **hantera förskapade entiteter**.

    ![Entiteter sidan – Hantera fördefinierade entiteter](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. I **Lägg till fördefinierade entiteter** dialogrutan klickar du på den fördefinierade entiteten som du vill lägga till (till exempel ”datetimeV2”). Klicka sedan på **Spara**.

    ![Fördefinierade entitet dialogrutan Lägg till](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Använda ett antal fördefinierade entiteten
När en entitet som är färdiga ingår i ditt program, med dess förutsägelser i ditt publicerade program. Beteendet för förskapade entiteter är förtränade och **kan** ändras. Följ dessa steg om du vill se hur det fungerar en fördefinierade entitet:

1. Lägg till en **nummer** entitet till appen, sedan [träna](luis-interactive-test.md) och [publicera](luis-how-to-publish-app.md) appen.
2. Klicka på slutpunkts-URL i den **publicera appen** kan du öppna LUIS-slutpunkten i en webbläsare. 
3. Lägg till ett uttryck till den URL som innehåller ett numeriskt uttryck. Du kan till exempel skriva i `buy two plane ticktets`, och se som identifierar LUIS `two` som en `builtin.number` entitet, och identifierar `2` som värdet på den `resolution` fält. Den `resolution` fältet hjälper dig att lösa tal och datum till ett kanoniskt format som är lättare för klientprogrammet att använda. 

    ![uttryck i webbläsare som innehåller ett antal entitet](./media/luis-use-prebuilt-entity/browser-query.png)

LUIS kan smart känna igen tal som inte ligger i icke-standardläge form. Prova att använda olika numeriska uttryck i din yttranden och se vad LUIS returnerar.

I följande exempel visar ett JSON-svar från LUIS, som innehåller av lösningen på värdet 24, för uttryck ”24”.

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
## <a name="use-a-prebuilt-datetimev2-entity"></a>Använda en fördefinierade datetimeV2 entitet
Den **datetimeV2** fördefinierade entitet identifierar datum, tider, datumintervall och tidsvaraktigheter. Följ dessa steg för att se hur `datetimeV2` fördefinierade entiteten fungerar:

1. Lägg till en **datetimeV2** entitet till appen, sedan [träna](luis-interactive-test.md) och [publicera](luis-how-to-publish-app.md) appen.
2. Klicka på slutpunkts-URL i den **publicera appen** kan du öppna LUIS-slutpunkten i en webbläsare. 
3. Lägg till ett uttryck till den URL som innehåller ett datumintervall. Du kan till exempel skriva i `book a flight tomorrow`, och se som identifierar LUIS `tomorrow` som en `builtin.datetimeV2.date` entitet, och identifierar morgondagens datum som värde i den `resolution` fält. 

I följande exempel visas hur JSON-svar från LUIS kan se ut om dagens datum och med 31 oktober-2017.

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
