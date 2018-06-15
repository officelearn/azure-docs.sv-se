---
title: Etiketten föreslagna utterances med THOMAS | Microsoft Docs
description: Använda språk förstå (THOMAS) att märka föreslagna utterances och öka active maskininlärning.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2017
ms.author: v-geberr
ms.openlocfilehash: 91a2fe738743d359677392bfb79aac885702b440
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356430"
---
# <a name="review-endpoint-utterances"></a>Granska endpoint utterances

Funktionen banbrytande i THOMAS är den [konceptet](luis-concept-review-endpoint-utterances.md) av aktiva learning. När din THOMAS har endpoint frågor, använder THOMAS active Lär dig för att förbättra kvaliteten på resultaten. Pågående active learning THOMAS undersöker alla endpoint utterances och väljer utterances att den är osäker på. Om du anger dessa utterances träna och publicera sedan THOMAS identifierar utterances mer exakt. 

## <a name="filter-utterances"></a>Filtrera utterances
1. Öppna din app (till exempel TravelAgent) genom att markera namnet på **Mina appar** sidan och välj sedan **skapa** i det översta fältet.

2. Under den **förbättra prestanda för**väljer **granska endpoint utterances**.

    ![Granska utterances](./media/label-suggested-utterances/review.png)

3. På den **granska endpoint utterances** väljer i den **filtrera listan efter avsikt eller entiteten** textruta. Den här listan innehåller alla avsikter under **INTENTS** och alla entiteter under **ENTITETER**.

    ![Utterances filter](./media/label-suggested-utterances/filter.png)

4. Välj en kategori (avsikter eller entiteter) i den nedrullningsbara listan och granska utterances.

    ![Avsiktshantering utterances](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etikett entiteter
THOMAS ersätts entitetsnamn bara entitet token (ord). Om en utterance har namnlösa entiteter, kan du märka dem i utterance. 

1. Välj ord i utterance. 

2. Välj en enhet i listan.

    ![Etikett entitet](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Justera enstaka utterance

Varje utterance har en föreslagna avsikten som visas i den **justerad avsikt** kolumn. 

1. Om du samtycker till att förslag Välj på kryssmarkeringen.

    ![Behåll justerade avsikt](./media/label-suggested-utterances/align-intent-check.png)

2. Om du inte samtycker till förslaget, Välj rätt avsikten i justerade avsiktshantering nedrullningsbara listan, och välj sedan på kryssmarkeringen till höger om justerade avsikten. 

    ![Justera avsikt](./media/label-suggested-utterances/align-intent.png)

3. När du har valt på kryssmarkeringen tas i utterance bort från listan. 

## <a name="align-several-utterances"></a>Justera flera utterances

Om du vill justera flera utterances kryssrutan till vänster om utterances och välj sedan på den **Lägg till valda** knappen. 

![Justera flera](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Kontrollera justerade avsikt
Du kan verifiera utterance var justerad med rätt syfte genom att gå till den **Intents** sidan, Välj avsiktshantering namnet och granska utterances. Utterance från **granska endpoint utterances** i listan.

## <a name="delete-utterance"></a>Ta bort utterance
Varje utterance kan tas bort från listan över granska. När tagits bort kan visas den inte i listan igen. Detta gäller även om användaren anger samma utterance från slutpunkten. 

Om du är osäker på om bör du ta bort utterance, antingen flytta avsiktshantering, NONE eller skapa en ny avsikt, till exempel ”övriga” och flytta utterance till det syftet. 

## <a name="delete-several-utterances"></a>Ta bort flera utterances
Om du vill ta bort flera utterances markerar varje objekt och väljer på Papperskorgen till höger om den **Lägg till valda** knappen.

![Ta bort flera](./media/label-suggested-utterances/delete-several.png)

## <a name="next-steps"></a>Nästa steg

Om du vill testa hur det fungerar bättre när du etikett föreslagna utterances, du kan komma åt konsolen test genom att välja **testa** i den övre panelen. Anvisningar om hur du testar din app med hjälp av test-konsolen finns [tåg och testa din app](Train-Test.md).
