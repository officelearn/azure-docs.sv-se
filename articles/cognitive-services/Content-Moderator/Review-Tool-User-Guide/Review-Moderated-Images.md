---
title: Använda innehålls granskningar via gransknings verktyget – Content Moderator
titleSuffix: Azure Cognitive Services
description: Lär dig hur gransknings verktyget tillåter att mänskliga moderatorer granskar bilder i en webb Portal.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 53d06f84172da0c4717cabe79f4f5152e73e9555
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754273"
---
# <a name="create-human-reviews"></a>Skapa mänsklig granskning

I den här guiden får du lära dig hur du konfigurerar [granskningar](../review-api.md#reviews) på webbplatsen för gransknings verktyget. Granskar lagring och visning av innehåll för de mänskliga moderatorerna att utvärdera. Moderatorer kan ändra de tillämpade taggarna och tillämpa egna anpassade taggar efter behov. När en användare slutför en granskning skickas resultatet till en angiven slut punkt för återanrop och innehållet tas bort från platsen.

## <a name="prerequisites"></a>Krav

- Logga in eller skapa ett konto på webbplatsen för Content Moderator [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="image-reviews"></a>Bildomdömen

1. Gå till [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/), Välj fliken **försök** och ladda upp några bilder att granska.
1. När de överförda bilderna har bearbetats går du till fliken **Granska** och väljer **bild**.

    ![Chrome-webbläsaren som visar gransknings verktyget med alternativet granska avbildning markerat](images/review-images-1.png)

    Bilderna visas med etiketter som har tilldelats av den automatiska moderator processen. De avbildningar som du har skickat via gransknings verktyget är inte synliga för andra granskare.

1. Du kan också flytta **granskningarna för att Visa** skjutreglaget (1) för att justera antalet bilder som visas på skärmen. Klicka på de **märkta** eller omärkta knapparna (2) för att sortera avbildningarna efter behov. Klicka på en etikett panel (3) om du vill aktivera eller inaktivera den.

    ![Chrome-webbläsare som visar gransknings verktyget med taggade bilder för granskning](images/review-images-2.png)

1. Om du vill se mer information om en bild klickar du på ellipsen i miniatyren och väljer **Visa information**. Du kan tilldela en bild till ett under team med alternativet **Flytta till** (mer information om under grupper finns i avsnittet [team](./configure.md#manage-team-and-subteams) ).

    ![En bild med alternativet Visa detaljer markerat](images/review-images-3.png)

1. Bläddra igenom bildens moderator information på informations sidan.

    ![En bild med moderator information som visas i ett separat fönster](images/review-images-4.png)

1. När du har granskat och uppdaterat tag gen tilldelningarna efter behov klickar du på **Nästa** för att skicka in dina granskningar. När du har skickat har du ungefär fem sekunder på dig att klicka **på föregående för** att gå tillbaka till föregående skärm och granska bilderna igen. Därefter är avbildningarna inte längre i sändnings kön och knappen **föregående** är inte längre tillgänglig.

## <a name="text-reviews"></a>Textomdömen

Text granskningar fungerar på samma sätt som granskning av bilder. I stället för att ladda upp innehåll kan du helt enkelt skriva eller klistra in text (upp till 1 024 tecken). Sedan analyserar Content Moderator texten och använder taggar (förutom annan kontroll information, till exempel svordomar och person uppgifter). I text granskningar kan du växla över tillämpade Taggar och/eller använda anpassade taggar innan du skickar granskningen.

![Skärm bild av gransknings verktyget som visar flaggad text i ett Chrome-webbläsarfönster](../images/reviewresults_text.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du konfigurerar och använder granskningar från [verktyget](https://contentmoderator.cognitive.microsoft.com)för Content moderator granskning. Sedan läser du [rest Apis guide](../try-review-api-review.md) eller [.NET SDK-guiden](../moderation-reviews-quickstart-dotnet.md) för att lära dig hur du skapar recensioner program mässigt.