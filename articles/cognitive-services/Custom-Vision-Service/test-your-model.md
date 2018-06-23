---
title: Testa och träna om en kognitiva modell - anpassad Vision Service - tjänster | Microsoft Docs
description: Lär dig mer om att testa en avbildning och sedan använda den för att träna om modellen.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 1933b1a45844ac99308baebe59b49687a957abfa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353280"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testa och träna om en modell med anpassade Vision Service

När du träna din modell kan du snabbt testa den med hjälp av en lokalt lagrad bild eller en avbildning online. Testet använder nyligen utbildade iteration.

## <a name="test-your-model"></a>Testa din modell

1. Från den [anpassad Vision webbsida](https://customvision.ai), Välj ditt projekt. Välj **snabbt testa** till höger på menyraden överst. Den här åtgärden öppnar ett fönster med etiketten **snabbt testa**.

    ![Knappen snabbt testa visas i det övre högra hörnet i fönstret.](./media/test-your-model/quick-test-button.png)

2. I den **snabbt testa** klickar du i den **skicka bilden** och anger URL till den bild som du vill använda för testet. Om du vill använda en lokalt lagrad avbildning istället klickar du på den **Bläddra lokala filer** och välj en lokal fil.

    ![Bild av sidan Skicka bild](./media/test-your-model/submit-image.png)

Den valda bilden visas i mitten på sidan. Resultatet visas under bilden i form av en tabell med två kolumner, med etiketten **taggar** och **förtroende**. När du visar resultaten, kan du stänga den **snabbt testa** fönster.

Du kan nu lägga till den här testavbildningen i modellen och träna om din modell.

## <a name="use-the-predicted-image-for-training"></a>Använd den förväntade avbildningen för utbildning.

Om du vill använda den avbildning som har skickats tidigare för träning, använder du följande steg:

1. Om du vill visa bilder som skickats till klassificeraren, öppna den [anpassad Vision webbsida](https://customvision.ai) och välj den __förutsägelser__ fliken.

    ![Bild av fliken förutsägelser](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Standardvyn visar avbildningar från den aktuella iterationen. Du kan använda den __Iteration__ listrutan fält som du vill visa bilder som skickats under föregående iterationer.

2. Hovra över en bild för att se vilka taggar som har förutsade av klassificeraren.

    > [!TIP]
    > Bilder rangordnas så att de avbildningar som kan ge de flesta vinster klassificeraren visas överst. Markera en annan sortering med hjälp av __sortera__ avsnitt.

    Om du vill lägga till en bild utbildningsdata, Välj avbildningen, markera taggen och välj sedan __spara och Stäng__. Bilden tas bort från __förutsägelser__ och läggs till i utbildning-avbildningar. Du kan visa den genom att välja den __utbildning bilder__ fliken.

    ![Bild av sidan märkning](./media/test-your-model/tag-image.png)

3. Använd den __Train__ för att träna om klassificeraren.

## <a name="next-steps"></a>Nästa steg

[Förbättra dina klassificerare](getting-started-improving-your-classifier.md)