---
title: Granska användarens yttranden-LUIS
titleSuffix: Azure Cognitive Services
description: Active Learning samlar in slut punkts frågor och väljer användarens slut punkt yttranden att det är osäkert. Du kan granska dessa yttranden för att välja avsikten och markera entiteter för dessa Read-World-yttranden. Acceptera ändringarna i ditt exempel yttranden och träna och publicera. LUIS identifierar sedan yttranden mer noggrant.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d5652857f2f35e392d3f512001044fd06bc0a0c9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499076"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Så här granskar du slut punkts yttranden i LUIS-portalen för aktiv inlärning

[Active Learning](luis-concept-review-endpoint-utterances.md) samlar in slut punkts frågor och väljer användarens slut punkt yttranden att det är osäkert. Du kan granska dessa yttranden för att välja avsikten och markera entiteter för dessa Read-World-yttranden. Acceptera ändringarna i ditt exempel yttranden och träna och publicera. LUIS identifierar sedan yttranden mer noggrant.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>Aktivera aktiv inlärning

Logga användar frågor för att aktivera aktiv inlärning. Detta åstadkommer du genom att ställa in [slut punkts frågan](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) med parametern och värdet för `log=true` QueryString.

## <a name="disable-active-learning"></a>Inaktivera aktiv inlärning

Du inaktiverar aktiv inlärning genom att inte logga användar frågor. Detta åstadkommer du genom att ställa in [slut punkts frågan](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) med parametern och värdet för `log=false` QueryString.

## <a name="filter-utterances"></a>Filtrera yttranden

1. Öppna din app (till exempel TravelAgent) genom att välja namnet på sidan **Mina appar** och välj sedan **skapa** i det översta fältet.

1. Under **förbättra appens prestanda**väljer du **gransknings slut punkt yttranden**.

1. På sidan **Granska slut punkts yttranden** väljer du text rutan **filter lista efter avsikt eller entitet** . Den här nedrullningsbara listan innehåller alla avsikter under **syften** och alla entiteter under **entiteter**.

    ![Yttranden-filter](./media/label-suggested-utterances/filter.png)

1. Välj en kategori (avsikter eller entiteter) i den nedrullningsbara listan och granska yttranden.

    ![Avsikts yttranden](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etikett enheter
LUIS ersätter entitets-token (ord) med entitetsnamn markerade i blått. Om en uttryck har omärkta entiteter, kan du märka dem i uttryck. 

1. Välj ett eller flera ord i uttryck. 

1. Välj en entitet i listan.

    ![Etiketten het](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Justera enskilda uttryck

Varje uttryck har en föreslagen avsikt som visas i kolumnen **justerad avsikt** . 

1. Om du samtycker till det förslaget väljer du på bock markeringen.

    ![Behåll justerad avsikt](./media/label-suggested-utterances/align-intent-check.png)

1. Om du inte stämmer överens med förslaget väljer du rätt avsikt från List rutan justerad avsikt och markerar sedan kryss rutan till höger om det justerade avsikts alternativet. 

    ![Justera avsikt](./media/label-suggested-utterances/align-intent.png)

1. När du har markerat kryss rutan tas uttryck bort från listan. 

## <a name="align-several-utterances"></a>Justera flera yttranden

Om du vill justera flera yttranden markerar du kryss rutan till vänster om yttranden och väljer sedan knappen **Lägg till markerad** . 

![Justera flera](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Kontrol lera justerings avsikt

Du kan kontrol lera att uttryck har justerats med rätt avsikt genom att gå till sidan **avsikter** , välja namnet på avsikten och granska yttranden. Uttryck från **gransknings slut punkts yttranden** finns i listan.

## <a name="delete-utterance"></a>Ta bort uttryck

Varje uttryck kan tas bort från gransknings listan. När du har tagit bort den visas den inte i listan igen. Detta gäller även om användaren anger samma uttryck från slut punkten. 

Om du är osäker på om du ska ta bort uttryck, antingen flyttar du den till none-avsikten eller skapar en ny avsikt, till exempel "Diverse" och flyttar uttryck till den avsikten. 

## <a name="delete-several-utterances"></a>Ta bort flera yttranden

Om du vill ta bort flera yttranden markerar du varje objekt och väljer på pappers korgen till höger om knappen **Lägg till markerad** .

![Ta bort flera](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Nästa steg

Om du vill testa hur prestanda förbättras när du har märkt föreslagen yttranden kan du komma åt test konsolen genom att välja **test** i den övre panelen. Anvisningar om hur du testar din app med hjälp av test konsolen finns i [träna och testa din app](luis-interactive-test.md).
