---
title: Granska användaryttranden - LUIS
titleSuffix: Azure Cognitive Services
description: Granska yttranden som fångas upp av aktiv inlärning för att välja avsikt och markera entiteter för read-world yttranden; acceptera ändringar, träna och publicera.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219856"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Så här förbättrar du LUIS-appen genom att granska slutpunktsyttranden

Processen att granska slutpunktsyttranden för korrekta förutsägelser kallas [Aktiv inlärning](luis-concept-review-endpoint-utterances.md). Aktiv inlärning samlar in slutpunktsfrågor och väljer användarens slutpunktsyttranden som den är osäker på. Du granskar dessa yttranden för att välja avsikt och markera entiteter för dessa läsvärldsyttranden. Acceptera dessa ändringar i dina exempelyttranden och träna och publicera. LUIS identifierar sedan yttranden mer exakt.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Aktivera aktivt lärande

Om du vill aktivera aktiv inlärning måste du logga användarfrågor. Detta åstadkoms genom att anropa `log=true` [slutpunktsfrågan](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) med frågesträngsparametern och värdet.

Använd LUIS-portalen för att skapa rätt slutpunktsfråga.

1. Välj din app i listan över appar i [luis-portalen.](https://preview.luis.ai/)
1. Gå till avsnittet **Hantera** och välj sedan **Azure-resurser**.
1. För den tilldelade förutsägelseresursen väljer du **Ändra frågeparametrar**.

    > [!div class="mx-imgBorder"]
    > ![Använd LUIS-portalen för att spara loggar, vilket krävs för aktiv inlärning.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Växla **Spara loggar** spara sedan genom att välja **Klar**.

    > [!div class="mx-imgBorder"]
    > ![Använd LUIS-portalen för att spara loggar, vilket krävs för aktiv inlärning.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Den här åtgärden ändrar `log=true` exempel-URL:en genom att lägga till frågestringsparametern. Kopiera och använd den ändrade exempelfråge-URL:en när du gör förutsägelsefrågor till slutpunkten för körning.

## <a name="correct-intent-predictions-to-align-utterances"></a>Korrigera avsiktsförutsägelser för att justera yttranden

Varje uttryck har en föreslagen avsikt som visas i kolumnen **Justerad avsikt.**

> [!div class="mx-imgBorder"]
> [![Granska slutpunktsyttranden som LUIS är osäker på](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Om du håller med om den avsikten markerar du markeringen. Om du inte håller med om förslaget väljer du rätt avsikt i listrutan justerad avsikt och markerar sedan markeringen till höger om den justerade avsikten. När du har markerat markeringen flyttas uttrycket till avsikten och tas bort från listan **Granska slutpunktsuttryck.**

> [!TIP]
> Det är viktigt att gå till sidan Avsiktsinformation för att granska och korrigera entitetsförutsägelserna från alla exempelyttranden från listan **Granska slutpunktsutsägelseer.**

## <a name="delete-utterance"></a>Ta bort uttryck

Varje uttryck kan tas bort från granskningslistan. När den har tagits bort visas den inte i listan igen. Detta gäller även om användaren anger samma uttryck från slutpunkten.

Om du är osäker på om du ska ta bort uttrycket flyttar du `miscellaneous` det antingen till avsikten Ingen eller skapar en ny avsikt, till exempel och flyttar uttrycket till den avsikten.

## <a name="disable-active-learning"></a>Inaktivera aktiv inlärning

Logga inte användarfrågor för att inaktivera aktiv inlärning. Detta åstadkoms genom att ange `log=false` [slutpunktsfrågan](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) med frågesträngsparametern och värdet eller inte använda frågesträngsvärdet eftersom standardvärdet är falskt.

## <a name="next-steps"></a>Nästa steg

Om du vill testa hur prestanda förbättras när du har märkt föreslagna uttryck kan du komma åt testkonsolen genom att välja **Testa** på den övre panelen. Instruktioner om hur du testar appen med testkonsolen finns i [Träna och testa appen](luis-interactive-test.md).
