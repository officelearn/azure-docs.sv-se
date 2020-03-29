---
title: Använd innehållsgranskningar via granskningsverktyget - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Lär dig hur granskningsverktyget gör det möjligt för mänskliga moderatorer att granska bilder i en webbportal.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044121"
---
# <a name="create-human-reviews"></a>Skapa mänskliga recensioner

I den här guiden får du lära dig hur du ställer in [recensioner](../review-api.md#reviews) på webbplatsen för granskningsverktyg. Recensioner lagra och visa innehåll för mänskliga moderatorer att bedöma. Moderatorer kan ändra de använda taggarna och använda sina egna anpassade taggar efter behov. När en användare slutför en granskning skickas resultaten till en angiven motringningsslutpunkt och innehållet tas bort från webbplatsen.

## <a name="prerequisites"></a>Krav

- Logga in eller skapa ett konto på webbplatsen för [granskning](https://contentmoderator.cognitive.microsoft.com/) av innehållsmoderator.

## <a name="image-reviews"></a>Bildomdömen

1. Gå till [granskningsverktyget,](https://contentmoderator.cognitive.microsoft.com/)välj fliken **Försök** och ladda upp några bilder som ska granskas.
1. När de uppladdade bilderna har avslutats går du till fliken **Granska** och väljer **Bild**.

    ![Webbläsaren Chrome som visar granskningsverktyget med alternativet Granska bild markerat](images/review-images-1.png)

    Bilderna visas med alla etiketter som har tilldelats av den automatiska modereringsprocessen. Bilderna som du har skickat in via granskningsverktyget är inte synliga för andra granskare.

1. Du kan också flytta skjutreglaget **Recensioner för att visa** (1) för att justera antalet bilder som visas på skärmen. Klicka på de **taggade** eller **otaggade** knapparna (2) för att sortera bilderna därefter. Klicka på en taggpanel (3) för att aktivera eller inaktivera den.

    ![Webbläsaren Chrome som visar granskningsverktyget med taggade bilder för granskning](images/review-images-2.png)

1. Om du vill se mer information om en bild klickar du på ellipsen i miniatyren och väljer **Visa information**. Du kan tilldela en bild till ett underteam med alternativet **Flytta till** (se avsnittet [Teams](./configure.md#manage-team-and-subteams) om du vill veta mer om undergrupper).

    ![En bild med alternativet Visa information markerad](images/review-images-3.png)

1. Bläddra bland informationen om bildmoderering på informationssidan.

    ![En bild med modereringsinformation i ett separat fönster](images/review-images-4.png)

1. När du har granskat och uppdaterat taggtilldelningarna efter behov klickar du på **Nästa** för att skicka in dina recensioner. När du har skickat in har du ungefär fem sekunder på dig att klicka på **knappen Föregående för** att återgå till föregående skärm och granska bilder igen. Därefter finns bilderna inte längre i kön Skicka och knappen **Föregående** är inte längre tillgänglig.

## <a name="text-reviews"></a>Textomdömen

Text recensioner fungerar på samma sätt som bildrecensioner. I stället för att ladda upp innehåll skriver eller klistrar du helt enkelt in i text (upp till 1 024 tecken). Innehållsmoderator analyserar sedan texten och tillämpar taggar (utöver annan modereringsinformation, till exempel svordomar och personuppgifter). I textgranskningar kan du växla de använda taggarna och/eller använda anpassade taggar innan du skickar granskningen.

![Skärmbild av granskningsverktyget som visar flaggad text i ett webbläsarfönster i Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du konfigurerar och använder recensioner från [verktyget Granskning](https://contentmoderator.cognitive.microsoft.com)av innehållsmodererator . Se sedan [REST API-guiden](../try-review-api-review.md) eller [snabbstarten .NET SDK](../dotnet-sdk-quickstart.md) för att lära dig hur du skapar recensioner programmässigt.