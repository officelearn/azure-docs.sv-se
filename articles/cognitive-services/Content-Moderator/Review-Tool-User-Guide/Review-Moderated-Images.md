---
title: Använd content granskningar via granskningsverktyget - Content Moderator
titlesuffix: Azure Cognitive Services
description: Lär dig hur granskningsverktyget hjälper mänskliga moderatorer att granska bilder i en webbportal.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629059"
---
# <a name="create-human-reviews"></a>Skapa mänsklig granskning

I den här guiden får du lära dig hur du ställer in [granskar](../review-api.md#reviews) på webbplatsen granska verktyget. Granskningar lagra och visa innehållet för mänskliga moderatorer att utvärdera. Moderatorer kan ändra de tillämpade taggarna och lägga till sina egna anpassade taggar efter behov. När en användare har slutfört en granskning, resultaten skickas till en slutpunkt för angivna återanrop och innehållet tas bort från platsen.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Logga in eller skapa ett konto på Content Moderator [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) plats.

## <a name="image-reviews"></a>Bildomdömen

1. Gå till den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/)väljer den **försök** fliken och ladda upp vissa bilder för att granska.
1. När bearbetningen är klar med de överförda bilderna, går du till den **granska** fliken och markera **bild**.

    ![Webbläsaren Chrome som visar granskningsverktyget med alternativet granska bild markerat](images/review-images-1.png)

    Det visas bilderna med etiketter som har tilldelats av automatisk moderering process. Bilder som du har skickat in via granskningsverktyget visas inte för andra granskare.

1. Du kan också flytta den **granskningar för att visa** skjutreglaget (1) för att justera antalet bilder som visas på skärmen. Klicka på den **taggade** eller **ej taggade** knappar (2) för att sortera avbildningarna på lämpligt sätt. Klicka på en panel taggen (3) att växla den eller inaktivera.

    ![Webbläsaren Chrome som visar granskningsverktyget med taggade bilder för granskning](images/review-images-2.png)

1. Om du vill se mer information om en bild, klicka på ellipsknappen i miniatyr och välj **visa information om**. Du kan tilldela en avbildning till en undergrupper med den **flytta till** alternativet (se den [team](./configure.md#manage-team-and-subteams) att ta reda på mer om undergrupper).

    ![En bild med alternativet Visa information om markerad](images/review-images-3.png)

1. Bläddra moderering bildinformation på sidan.

    ![En bild med moderering information som visas i ett separat fönster](images/review-images-4.png)

1. När du har granskat och uppdatera taggen tilldelningar efter behov, klickar du på **nästa** att skicka din granskningar. När du skickar det behöver cirka fem sekunder klickar du på den **föregående** vill gå till föregående sida och granska avbildningar igen. Efter det bilderna inte längre skicka kön och **föregående** knappen är inte längre tillgänglig.

## <a name="text-reviews"></a>Textomdömen

Text granskar funktion på samma sätt som bild granskningar. I stället för att överföra innehåll du helt enkelt skriva eller klistra in text (upp till 1 024 tecken). Content Moderator analyserar texten och tillämpar taggar (utöver andra moderering information, t.ex svordomar och personliga data). I textomdömen, du kan växla tillämpade taggar och/eller lägga till anpassade taggar innan du skickar granskningen.

![Skärmbild av granska verktyget visar flaggade text i ett webbläsarfönster för Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Nästa steg

I den här handboken beskrivs hur du skapar och använder granskningar från Content Moderator [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com). Sedan kan du se den [REST API-guiden](../try-review-api-review.md) eller [.NET SDK guide](../moderation-reviews-quickstart-dotnet.md) att lära dig hur du skapar granskningar programmässigt.