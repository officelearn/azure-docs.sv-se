---
title: Fördefinierade modeller för språkförståelse
titleSuffix: Azure Cognitive Services
description: LUIS innehåller en uppsättning färdiga modeller för att snabbt lägga till vanliga, konversationsanvändarscenarier.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013589"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Lägga till färdiga modeller för vanliga användningsscenarier 

LUIS innehåller en uppsättning färdiga modeller för att snabbt lägga till vanliga, konversationsanvändarscenarier. Detta är ett snabbt och enkelt sätt att lägga till förmågor till din konversation klient program utan att behöva utforma modeller för dessa förmågor. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Lägga till en fördefinierad domän

1. Välj din app på sidan **Mina appar.** Då öppnas appen för avsnittet **Skapa** i appen. 

1. Välj **Fördefinierade domäner** i det vänstra verktygsfältet. 

1. Leta reda på den domän som du vill lägga till i appen och välj sedan **knappen Lägg till domän.**

    > [!div class="mx-imgBorder"]
    > ![Lägga till fördefinierad domän för kalender](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Lägga till en fördefinierad avsikt

1. Välj din app på sidan **Mina appar.** Då öppnas appen för avsnittet **Skapa** i appen. 

1. På sidan **Avsikter** väljer du **Lägg till fördefinierad domänavsikt** i verktygsfältet ovanför avsiktslistan. 

1. Välj avsikten **Utilities.Cancel** i popup-dialogrutan. 

    > [!div class="mx-imgBorder"]
    > ![Lägg till fördefinierad avsikt](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Välj knappen **Klar.**

## <a name="add-a-prebuilt-entity"></a>Lägga till en fördefinierad entitet

1. Öppna appen genom att klicka på namnet på sidan **Mina appar** och sedan klicka på **Entiteter** på vänster sida. 

1. Klicka på **Lägg till fördefinierade entiteter**på sidan **Entitet.**

1. Välj den fördefinierade entiteten i dialogrutan **Lägg till fördefinierade entiteter.** 

    > [!div class="mx-imgBorder"]
    > ![Dialogrutan Lägg till fördefinierad entitet](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Välj **Done** (Klar). När entiteten har lagts till behöver du inte träna appen. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicera för att visa fördefinierad modell från förutsägelseslutpunkten

Det enklaste sättet att visa värdet för en fördefinierad modell är att fråga från den publicerade slutpunkten. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entiteter som innehåller en fördefinierad entitetstoken
 
Om du har en datorinlärd entitet som begränsas av en fördefinierad entitet lägger du till en delkomponent i den datorinlärda entiteten och lägger sedan till en begränsning av en fördefinierad entitet.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Bygg modell från .csv med REST API:er](./luis-tutorial-node-import-utterances-csv.md)
