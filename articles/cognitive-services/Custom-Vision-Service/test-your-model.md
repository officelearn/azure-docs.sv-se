---
title: Testa och tränar en modell – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att testa en bild och sedan använda den för att träna modellen.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: d516cee81aef66ec58399cb5ff23c89db16bf2ab
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350444"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testa och tränar en modell med Custom Vision Service

När du träna din modell kan du snabbt testa den med en lokalt lagrad bild eller en avbildning online. Testet använder den nyligen tränade iterationen.

## <a name="test-your-model"></a>Testa din modell

1. Från den [Custom Vision-webbsida](https://customvision.ai), Välj ditt projekt. Välj **snabbtest** till höger i den övre menyraden. Den här åtgärden öppnar ett fönster som är märkt **snabbtest**.

    ![Knappen snabbtest visas i det övre högra hörnet i fönstret.](./media/test-your-model/quick-test-button.png)

2. I den **snabbtest** klickar du på i den **skicka avbildningen** fältet och ange Webbadressen till den avbildning som du vill använda för testet. Om du vill använda en lokalt lagrad bild i stället, klickar du på den **Bläddra bland lokala filer** och välj en lokal fil.

    ![Bild av sidan skicka avbildningen](./media/test-your-model/submit-image.png)

Den valda bilden visas i mitten på sidan. Resultaten visas under bilden i form av en tabell med två kolumner som är märkt **taggar** och **förtroende**. När du visar resultatet, du kan stänga den **snabbtest** fönster.

Du kan nu lägga till den här testbilden till din modell och sedan träna din modell.

## <a name="use-the-predicted-image-for-training"></a>Använda förutspådda avbildningen för träning

Om du vill använda den avbildning som har skickats tidigare för träning, använder du följande steg:

1. Om du vill visa bilder som skickas till klassificeraren, öppna den [Custom Vision-webbsida](https://customvision.ai) och välj den __förutsägelser__ fliken.

    ![Bild av fliken förutsägelser](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Standardvyn visar avbildningar från den aktuella iterationen. Du kan använda den __Iteration__ nedrullningsbar listruta fält som du vill visa bilder som har skickats under föregående iterationer.

2. Hovra över en bild att se vilka taggar som har uppskattad av klassificeraren.

    > [!TIP]
    > Bilder rangordnas så att de avbildningar som kan ge de flesta vinster klassificeraren är högst upp. Välj en annan sortering genom att använda den __sortera__ avsnittet.

    Om du vill lägga till en bild i dina utbildningsdata, Välj en avbildning med taggen och välj därefter __spara och Stäng__. Avbildningen tas bort från __förutsägelser__ och läggs till i inlärningsbilder. Du kan visa den genom att välja den __Inlärningsbilder__ fliken.

    ![Bild av sidan taggning](./media/test-your-model/tag-image.png)

3. Använd den __träna__ knappen för att träna om klassificeraren.

## <a name="next-steps"></a>Nästa steg

[Förbättra din klassificerare](getting-started-improving-your-classifier.md)
