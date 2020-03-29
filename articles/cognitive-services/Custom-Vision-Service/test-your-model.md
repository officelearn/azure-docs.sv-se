---
title: Testa och omskola en modell - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du testar en bild och sedan använder den för att omskola din modell i custom vision-tjänsten.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: c6ed8869f6d33609381a42fd22d728e9e5542802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73721196"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testa och omskola en modell med Custom Vision Service

När du har tränat din modell kan du snabbt testa den med en lokalt lagrad bild eller en onlinebild. Testet använder den senast tränade iterationen.

## <a name="test-your-model"></a>Testa din modell

1. Välj projektet på [webbsidan Anpassad vision.](https://customvision.ai) Välj **Snabbtest** till höger om den övre menyraden. Den här åtgärden öppnar ett fönster med etiketten **Snabbtest**.

    ![Snabbtestknappen visas i det övre högra hörnet av fönstret.](./media/test-your-model/quick-test-button.png)

2. I fönstret **Snabbtest** klickar du i fältet **Skicka bild** och anger webbadressen till den bild som du vill använda för testet. Om du vill använda en lokalt lagrad bild i stället klickar du på knappen **Bläddra bland lokala filer** och väljer en lokal bildfil.

    ![Bild på sidan skicka bild](./media/test-your-model/submit-image.png)

Bilden du väljer visas mitt på sidan. Därefter visas resultaten under bilden i form av en tabell med två kolumner med etiketten **Taggar** och **Konfidens**. När du har visat resultaten kan du stänga **snabbtestfönstret.**

Du kan nu lägga till den här testavbildningen i din modell och sedan träna om din modell.

## <a name="use-the-predicted-image-for-training"></a>Använd den förväntade bilden för träning

Så här använder du bilden som skickats tidigare för utbildning:

1. Om du vill visa bilder som skickats till klassificeraren öppnar du [webbsidan Anpassad vision](https://customvision.ai) och väljer fliken __Förutsägelser.__

    ![Bild av fliken Förutsägelser](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Standardvyn visar bilder från den aktuella iterationen. Du kan använda listrutan __Iteration__ för att visa bilder som skickats in under tidigare iterationer.

2. Hovra över en bild för att se taggarna som förutspåddes av klassificeraren.

    > [!TIP]
    > Bilderna rangordnas, så att de bilder som kan ge mest vinster till klassificeraren är högst upp. Om du vill välja en annan sortering använder du avsnittet __Sortera.__

    Om du vill lägga till en bild i träningsdata markerar du bilden, markerar taggen och väljer sedan __Spara och stäng__. Bilden tas bort från __Förutsägelser__ och läggs till i träningsbilderna. Du kan visa den genom att välja fliken __Träningsbilder.__

    ![Bild på taggningssidan](./media/test-your-model/tag-image.png)

3. Använd __tågknappen__ för att träna klassificera.

## <a name="next-steps"></a>Nästa steg

[Förbättra din klassificerare](getting-started-improving-your-classifier.md)
