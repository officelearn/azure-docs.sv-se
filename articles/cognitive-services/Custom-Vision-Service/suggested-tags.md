---
title: Etikettbilder snabbare med Smart Labeler
titleSuffix: Azure Cognitive Services
description: I den här guiden får du lära dig hur du använder Smart Labeler för att generera föreslagna taggar för bilder. På så sätt kan du märka ett stort antal bilder snabbare när du tränar en Custom Vision-modell.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647758"
---
# <a name="label-images-faster-with-smart-labeler"></a>Etikettbilder snabbare med Smart Labeler

I den här guiden får du lära dig hur du använder Smart Labeler för att generera föreslagna taggar för bilder. På så sätt kan du märka ett stort antal bilder snabbare när du tränar en Custom Vision-modell.

När du taggar bilder för en Custom Vision-modell använder tjänsten den senast tränade iterationen av modellen för att förutsäga etiketterna för otaggade bilder. Den visar sedan dessa förutsägelser som föreslagna taggar, baserat på den valda konfidenströskeln och förutsägelseosäkerheten. Du kan sedan antingen bekräfta eller ändra förslagen, påskynda processen med att manuellt tagga bilderna för utbildning.

## <a name="when-to-use-smart-labeler"></a>När ska Smart Labeler användas

Tänk på följande:

* Du bör bara begära föreslagna taggar för bilder vars innehåll redan har tränats en gång. Få inte förslag på en ny tagg som du just har börjat träna.

> [!IMPORTANT]
> Smart Labeler-funktionen använder samma [prismodell](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) som vanliga förutsägelser. Första gången du utlöser föreslagna taggar för en uppsättning bilder debiteras du på samma sätt som för förutsägelsesamtal. Därefter lagrar tjänsten resultaten för de valda bilderna i en databas i 30 dagar, och du kan komma åt dem när som helst gratis inom den perioden. Efter 30 dagar debiteras du om du begär deras föreslagna taggar igen.

## <a name="smart-labeler-workflow"></a>Smart Labeler-arbetsflöde

Följande steg visar hur du använder Smart Labeler:

1. Ladda upp alla dina träningsbilder till ditt Custom Vision-projekt.
1. Märk en del av datauppsättningen och välj lika många bilder för varje tagg.
    > [!TIP]
    > Se till att du använder alla taggar som du vill ha förslag för senare.
1. Starta utbildningsprocessen.
1. När träningen är klar navigerar du till vyn **Otaggad** och väljer knappen **Hämta föreslagna taggar** i den vänstra rutan.
    > [!div class="mx-imgBorder"]
    > ![Knappen Föreslagna taggar visas under fliken otaggade bilder.](./media/suggested-tags/suggested-tags-button.png)
1. I popup-fönstret som visas anger du hur många bilder du vill ha förslag för. Du bör bara få inledande taggförslag för en del av de otaggade bilderna. Du får bättre tag förslag som du iterera genom denna process.
1. Bekräfta de föreslagna taggarna och åtgärda alla som inte är korrekta.
    > [!TIP]
    > Bilder med föreslagna taggar sorteras efter deras förutsägelseosäkerhet (lägre värden indikerar högre konfidens). Du kan ändra sorteringsordningen med alternativet **Sortera efter osäkerhet.** Om du ställer in ordern till **hög till låg**kan du korrigera förutsägelserna om hög osäkerhet först och sedan snabbt bekräfta de lågosäkerhetsprognoser.
    * I bildklassificeringsprojekt kan du välja och bekräfta taggar i batchar. Filtrera vyn efter en viss föreslagen tagg, avmarkera bilder som är fel taggade och bekräfta sedan resten i en batch.
        > [!div class="mx-imgBorder"]
        > ![Föreslagna taggar visas i batchläge för IC med filter.](./media/suggested-tags/ic-batch-mode.png)

        Du kan också använda föreslagna taggar i individuellt bildläge genom att välja en bild från galleriet.

        ![Föreslagna taggar visas i individuellt bildläge för IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * I objektidentifieringsprojekt stöds inte batchbekräftelser, men du kan fortfarande filtrera och sortera efter föreslagna taggar för en mer organiserad märkningsupplevelse. Miniatyrer av dina otaggade bilder visar ett överlägg av markeringsrutor som anger var föreslagna taggar finns. Om du inte markerar ett föreslaget taggfilter visas alla otaggade bilder utan att markeringsrutorna läggs över.
        > [!div class="mx-imgBorder"]
        > ![Föreslagna taggar visas i batchläge för OD med filter.](./media/suggested-tags/od-batch-mode.png)

        Om du vill bekräfta taggar för objektidentifiering måste du använda dem på varje enskild bild i galleriet.

        ![Föreslagna taggar visas i individuellt bildläge för OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Starta träningsprocessen igen.
1. Upprepa föregående steg tills du är nöjd med förslagskvaliteten.

## <a name="next-steps"></a>Nästa steg

Följ en snabbstart för att komma igång med att skapa och träna ett Custom Vision-projekt.

* [Skapa en klassificerare](getting-started-build-a-classifier.md)
* [Skapa en objektidentifierare](get-started-build-detector.md)