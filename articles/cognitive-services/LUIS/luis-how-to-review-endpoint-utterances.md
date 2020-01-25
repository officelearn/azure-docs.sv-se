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
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: ed0b9450217b06ff145641d9e268ccee28ee49b2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710598"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Förbättra LUIS-appen genom att granska slut punkts yttranden

Processen för att granska slut punkts yttranden för korrekta förutsägelser kallas [aktiv inlärning](luis-concept-review-endpoint-utterances.md). Active Learning samlar in slut punkts frågor och väljer användarens slut punkt yttranden att det är osäkert. Du kan granska dessa yttranden för att välja avsikten och markera entiteter för dessa Read-World-yttranden. Acceptera ändringarna i ditt exempel yttranden och träna och publicera. LUIS identifierar sedan yttranden mer noggrant.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Aktivera aktiv inlärning

Om du vill aktivera aktiv inlärning måste du logga användar frågor. Detta åstadkommer du genom att anropa [slut punkts frågan](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) med parametern och värdet för `log=true` QueryString.

## <a name="correct-intent-predictions-to-align-utterances"></a>Korrigera yttranden genom att korrigera avsikts förutsägelser

Varje uttryck har en föreslagna avsikt som visas i den **justerad avsikt** kolumn.

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

Om du vill testa hur prestanda förbättras när du lagt till etiketter föreslagna yttranden, du kan komma åt konsolen test genom att välja **testa** i den övre panelen. Anvisningar om hur du testar din app med hjälp av test-konsolen finns i [träna och testa din app](luis-interactive-test.md).
