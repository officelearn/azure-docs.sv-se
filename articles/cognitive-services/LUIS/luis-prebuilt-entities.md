---
title: Fördefinierade entiteter för Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS innehåller en uppsättning fördefinierade entiteter för att identifiera vanliga typer av information som datum, tider, nummer, mätningar och valuta. Fördefinierade entitet support varierar beroende på kultur LUIS-appen.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: db0fb8962030b536b6ea73f4141da551434cd528
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560356"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Färdiga entiteter för att identifiera vanliga datatyper

LUIS innehåller en uppsättning fördefinierade entiteter för att identifiera vanliga typer av information som datum, tider, nummer, mätning av faktisk användning och valuta. 

## <a name="add-a-prebuilt-entity"></a>Lägg till en fördefinierade entitet

1. Öppna din app genom att klicka på namnet på **Mina appar** , och klicka sedan på **entiteter** till vänster. 

1. På den **entiteter** klickar du på **Lägg till fördefinierade entitet**.

1. I **Lägg till fördefinierade entiteter** dialogrutan väljer du datetimeV2 fördefinierade entitet. 

    ![Fördefinierade entitet dialogrutan Lägg till](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Välj **Done** (Klar).

## <a name="publish-the-app"></a>Publicera appen

Det enklaste sättet att visa värdet för en fördefinierade entitet är att frågan från den publicerade slutpunkten. 

1. I det översta verktygsfältet väljer **publicera**. Publicera till **produktion**. 

1. När meddelandet grönt visas väljer du den **finns i listan över slutpunkter** länken för att se slutpunkterna.

1. Välj en slutpunkt. En ny webbläsarflik öppnas till denna slutpunkt. Håll webbläsarfliken öppen och Fortsätt den **Test** avsnittet.

## <a name="test"></a>Testa
När entiteten har lagts till kan behöver du inte att träna appen. 

Testa nya avsikten vid slutpunkten av lagt till ett värde för den **q** parametern. Använd följande tabell för föreslagna yttranden för **q**:

|Testa uttryck|Värdet för entitet|
|--|:--|
|Boka en flygning imorgon|2018-10-19|
|avbryta bokningen 3 mars|LUIS returnerade de senaste 3 mars tidigare (2018-03-03) och mars 3 i framtiden (2019-03-03) eftersom uttryck inte har angett ett år.|
|Schemalägga ett möte kl. 10|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Markera entiteter som innehåller fördefinierade entitet-token
 Om du har text, till exempel `HH-1234`, som du vill markera som en anpassad entitet _och_ du har [fördefinierade nummer](luis-reference-prebuilt-number.md) lagts till modellen kan du inte längre att markera den anpassade entiteten i LUIS-portalen. Du kan markera med API: et. 

 För att markera den här typen av token, där en del av den redan har markerats med en fördefinierade entitet, tar du bort fördefinierade entiteten från LUIS-app. Du behöver inte skapa appen. Markera sedan token med en egen anpassad entitet. Lägg sedan till den fördefinierade entitet tillbaka till LUIS-app.

 För ett annat exempel bör du överväga uttryck som en lista över klass inställningar: `I want first year spanish, second year calculus, and fourth year english lit.`Om Luis-appen har det tillagda `first` `second`ordnings ordnings numret, `fourth` , och är redan markerat med ordnings tal. Om du vill avbilda ordningstalet och klassen kan du omsluta runt fördefinierade ordningstal och den anpassade entiteten för klassnamn skapar en sammansatt entitet.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Fördefinierade enhetsreferens](./luis-reference-prebuilt-entities.md)
