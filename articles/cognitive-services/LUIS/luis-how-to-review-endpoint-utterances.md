---
title: Granska användare yttranden
titleSuffix: Language Understanding - Azure Cognitive Services
description: Aktiv inlärning samlar in endpoint frågor och väljer användarens slutpunkt yttranden som det är osäker på. Du kan granska dessa uttryck för att välja avsikten och markera entiteter för dessa Läs-world yttranden. Acceptera ändringarna i ditt exempel yttranden och sedan träna och publicera. LUIS identifierar sedan yttranden som är mer exakt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 8fac360682ef11c438cdec333fac21d6f8cfc117
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895915"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Granska endpoint yttranden LUIS-portalen för aktiv inlärning

[Aktiv inlärning](luis-concept-review-endpoint-utterances.md) avbildas endpoint frågor och väljer användarens slutpunkt yttranden som det är osäker på. Du kan granska dessa uttryck för att välja avsikten och markera entiteter för dessa Läs-world yttranden. Acceptera ändringarna i ditt exempel yttranden och sedan träna och publicera. LUIS identifierar sedan yttranden som är mer exakt.


## <a name="enable-active-learning"></a>Aktivera aktiv inlärning

Om du vill aktivera aktiv inlärning logga användarfrågor. Detta åstadkoms genom att ange den [endpoint fråga](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) med den `log=true` querystring parametern och värdet.

## <a name="disable-active-learning"></a>Inaktivera aktiv inlärning

Om du vill inaktivera aktiv inlärning, loggas inte användarfrågor. Detta åstadkoms genom att ange den [endpoint fråga](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) med den `log=false` querystring parametern och värdet.

## <a name="filter-utterances"></a>Filtrera uttryck

1. Öppna din app (till exempel TravelAgent) genom att välja dess namn på **Mina appar** sidan och välj sedan **skapa** i det översta fältet.

1. Under den **förbättra apprestanda**väljer **granska endpoint yttranden**.

1. På den **granska endpoint yttranden** väljer i den **filtrera listan efter avsikt eller entitet** textrutan. Den här listan innehåller alla avsikter under **AVSIKTER** och alla entiteter under **ENTITETER**.

    ![Yttranden filter](./media/label-suggested-utterances/filter.png)

1. Välj en kategori (avsikter eller entiteter) i den nedrullningsbara listan och granska talade.

    ![Avsiktshantering yttranden](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etikett-entiteter
LUIS ersätter entitet token (ord) med entitetsnamn bara. Om ett uttryck har utan etikett entiteter, märker du dem i uttryck. 

1. Välj på orden i uttryck. 

1. Välj en entitet i listan.

    ![Etikett-entitet](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Justera enda uttryck

Varje uttryck har en föreslagna avsikt som visas i den **justerad avsikt** kolumn. 

1. Om du godkänner det förslaget, väljer du på kryssmarkeringen.

    ![Behåll justerade avsikt](./media/label-suggested-utterances/align-intent-check.png)

1. Om du inte samtycker till förslaget, Välj rätt avsikten i justerade avsikt nedrullningsbara listan och välj sedan på kryssmarkeringen till höger om justerade avsikten. 

    ![Justera avsikt](./media/label-suggested-utterances/align-intent.png)

1. När du har valt på kryssmarkeringen tas i uttryck bort från listan. 

## <a name="align-several-utterances"></a>Justera flera yttranden

Om du vill justera flera yttranden kryssrutan till vänster om talade och välj sedan på den **Lägg till vald** knappen. 

![Justera flera](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Kontrollera justerade avsikt

Du kan kontrollera uttryck har justerats med rätt avsikten genom att gå till den **avsikter** väljer du namnet på avsikt, och granska talade. Uttryck från **granska endpoint yttranden** i listan.

## <a name="delete-utterance"></a>Ta bort uttryck

Varje uttryck kan tas bort från listan över granskning. När bort kommer visas den inte i listan igen. Detta gäller även om användaren anger samma uttryck från slutpunkten. 

Om du är osäker på om bör du ta bort uttryck, flytta den till avsikt, ingen antingen eller skapa en ny avsikt, till exempel ”övriga” och flytta uttryck till detta syfte. 

## <a name="delete-several-utterances"></a>Ta bort flera yttranden

Om du vill ta bort flera yttranden, markerar du varje objekt och väljer på Papperskorgen till höger om den **Lägg till vald** knappen.

![Ta bort flera](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Nästa steg

Om du vill testa hur prestanda förbättras när du lagt till etiketter föreslagna yttranden, du kan komma åt konsolen test genom att välja **testa** i den övre panelen. Anvisningar om hur du testar din app med hjälp av test-konsolen finns i [träna och testa din app](luis-interactive-test.md).
