---
title: Färdiga modeller för Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS innehåller en uppsättning förbyggda modeller för att snabbt lägga till vanliga användar scenarier i konversationen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013589"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Lägg till fördefinierade modeller för vanliga användnings scenarier 

LUIS innehåller en uppsättning förbyggda modeller för att snabbt lägga till vanliga användar scenarier i konversationen. Det här är ett snabbt och enkelt sätt att lägga till funktioner i ditt samtals klient program utan att behöva utforma modeller för dessa förmågor. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Lägga till en fördefinierad domän

1. På den **Mina appar** väljer du din app. Din app öppnas den **skapa** i appen. 

1. Välj **fördefinierade domäner** i det vänstra verktygsfältet. 

1. Leta upp den domän som du vill lägga till i appen och välj sedan knappen **Lägg till domän** .

    > [!div class="mx-imgBorder"]
    > ![lägga till en fördefinierad domän för kalender](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Lägg till en fördefinierade avsikt

1. På den **Mina appar** väljer du din app. Din app öppnas den **skapa** i appen. 

1. På sidan **avsikter** väljer du **Lägg till fördefinierad domän avsikt** från verktygsfältet ovanför listan med intenter. 

1. Välj den **Utilities.Cancel** avsikt från popup-dialogruta. 

    > [!div class="mx-imgBorder"]
    > ![lägga till ett fördefinierat syfte](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Välj den **klar** knappen.

## <a name="add-a-prebuilt-entity"></a>Lägg till en fördefinierade entitet

1. Öppna din app genom att klicka på namnet på **Mina appar** , och klicka sedan på **entiteter** till vänster. 

1. På den **entiteter** klickar du på **Lägg till fördefinierade entitet**.

1. I dialog rutan **Lägg till fördefinierade entiteter** väljer du den fördefinierade entiteten. 

    > [!div class="mx-imgBorder"]
    > dialog rutan ![Lägg till fördefinierad entitet](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Välj **Done** (Klar). När entiteten har lagts till kan behöver du inte att träna appen. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicera för att Visa fördefinierad modell från förutsägelse slut punkt

Det enklaste sättet att visa värdet för en fördefinierad modell är att fråga från den publicerade slut punkten. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entiteter som innehåller en fördefinierad Entity-token
 
Om du har en enhet som har registrerats av enheten som är begränsad av en fördefinierad entitet, lägger du till en del komponent till den enhet som har lärts och lägger sedan till en begränsning för en fördefinierad entitet.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Bygg en modell från. csv med REST API: er](./luis-tutorial-node-import-utterances-csv.md)
