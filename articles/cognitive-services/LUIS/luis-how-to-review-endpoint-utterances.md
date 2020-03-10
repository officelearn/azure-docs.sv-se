---
title: Granska användarens yttranden-LUIS
titleSuffix: Azure Cognitive Services
description: Granska yttranden som har registrerats av aktiv inlärning och välj avsikt och markera entiteter för Read-World yttranden; acceptera ändringar, träna och publicera.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393781"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Förbättra LUIS-appen genom att granska slut punkts yttranden

Processen för att granska slut punkts yttranden för korrekta förutsägelser kallas [aktiv inlärning](luis-concept-review-endpoint-utterances.md). Active Learning samlar in slut punkts frågor och väljer användarens slut punkt yttranden att det är osäkert. Du kan granska dessa yttranden för att välja avsikten och markera entiteter för dessa Read-World-yttranden. Acceptera ändringarna i ditt exempel yttranden och träna och publicera. LUIS identifierar sedan yttranden mer noggrant.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Aktivera aktiv inlärning

Om du vill aktivera aktiv inlärning måste du logga användar frågor. Detta åstadkommer du genom att anropa [slut punkts frågan](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) med parametern och värdet för `log=true` QueryString.

Använd LUIS-portalen för att skapa rätt slut punkts fråga.

1. Välj din app i listan över appar i [Luis-portalen för förhands granskning](https://preview.luis.ai/).
1. Gå till avsnittet **Hantera** och välj sedan **Azure-resurser**.
1. För den tilldelade förutsägelse resursen väljer du **ändra**frågeparametrar.

    > [!div class="mx-imgBorder"]
    > ![använda LUIS-portalen för att spara loggar, vilket krävs för aktiv inlärning.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Växla **Spara loggar** och spara genom att välja **färdig**.

    > [!div class="mx-imgBorder"]
    > ![använda LUIS-portalen för att spara loggar, vilket krävs för aktiv inlärning.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Den här åtgärden ändrar exempel-URL genom att lägga till parametern `log=true` QueryString. Kopiera och Använd fråge-URL: en för den ändrade exemplet när du gör förutsägelse frågor till körnings slut punkten.

## <a name="correct-intent-predictions-to-align-utterances"></a>Korrigera yttranden genom att korrigera avsikts förutsägelser

Varje uttryck har en föreslagen avsikt som visas i kolumnen **justerad avsikt** .

> [!div class="mx-imgBorder"]
> [![granska slut punkten yttranden att LUIS är osäker på](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Om du samtycker till detta väljer du kryss rutan. Om du inte samtycker till förslaget, Välj rätt avsikten i justerade avsikt nedrullningsbara listan och välj sedan på kryssmarkeringen till höger om justerade avsikten. När du har markerat kryss rutan flyttas uttryck till avsikten och tas bort från listan **yttranden för gransknings slut punkt** .

> [!TIP]
> Det är viktigt att gå till sidan med information om avsikt att granska och korrigera enhets förutsägelserna från alla exempel yttranden från **yttranden för gransknings slut punkt** .

## <a name="delete-utterance"></a>Ta bort uttryck

Varje uttryck kan tas bort från listan över granskning. När bort kommer visas den inte i listan igen. Detta gäller även om användaren anger samma uttryck från slutpunkten.

Om du är osäker på om du ska ta bort uttryck, antingen flyttar du den till ingen avsikt eller skapar en ny avsikt, till exempel `miscellaneous` och flyttar uttryck till den avsikten.

## <a name="disable-active-learning"></a>Inaktivera aktiv inlärning

Du inaktiverar aktiv inlärning genom att inte logga användar frågor. Detta åstadkommer du genom att ställa in [slut punkts frågan](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) med parametern `log=false` QueryString och värdet eller inte använda QueryString-värdet eftersom standardvärdet är false.

## <a name="next-steps"></a>Nästa steg

Om du vill testa hur prestanda förbättras när du har märkt föreslagen yttranden kan du komma åt test konsolen genom att välja **test** i den övre panelen. Anvisningar om hur du testar din app med hjälp av test konsolen finns i [träna och testa din app](luis-interactive-test.md).
