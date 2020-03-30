---
title: Anpassa en brands-modell med webbplatsen Video Indexer
titleSuffix: Azure Media Services
description: Läs om hur du anpassar en Brands-modell med webbplatsen Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128046"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Anpassa en brands-modell med webbplatsen Video Indexer

Video Indexer stöder varumärkesidentifiering från tal- och visuell text vid indexering och omindexering av video- och ljudinnehåll. Varumärkesidentifieringsfunktionen identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bings varumärkesdatabas. Om Microsoft till exempel nämns i video- eller ljudinnehåll eller om det visas i visuell text i en video, identifierar Video Indexer det som ett varumärke i innehållet.

Med en anpassad Brands-modell kan du:

- välj om du vill att Video Indexer ska identifiera varumärken från Bings varumärkesdatabas.
- välj om du vill att Video Indexer ska utesluta vissa varumärken från att upptäckas (i huvudsak skapa en neka lista över varumärken).
- Välj om du vill att Video Indexer ska inkludera varumärken som ska ingå i din modell som kanske inte finns i Bings varumärkesdatabas (i huvudsak skapar en accepterande lista över varumärken).

En detaljerad översikt finns i den här [översikten](customize-brands-model-overview.md).

Du kan använda webbplatsen Video Indexer för att skapa, använda och redigera anpassade varumärken modeller som identifieras i en video, enligt beskrivningen i det här avsnittet. Du kan också använda API:et enligt beskrivningen i [modellen Anpassa varumärken med API:er](customize-brands-model-with-api.md).

## <a name="edit-brands-model-settings"></a>Redigera modellinställningar för Varumärken

Du har möjlighet att ange om du vill att varumärken från Bing-varumärkesdatabasen ska identifieras eller inte. Om du vill ange det här alternativet måste du redigera inställningarna för modellen Brands. Följ de här stegen:

1. Gå till [videoindexerarens](https://www.videoindexer.ai/) webbplats och logga in.
2. Om du vill anpassa en modell i ditt konto väljer du **anpassningsknappen Innehållsmodell** längst upp till höger på sidan.

   ![Anpassa innehållsmodellen i Video Indexer](./media/content-model-customization/content-model-customization.png)

3. Om du vill redigera varumärken väljer du fliken **Varumärken.**

    ![Anpassa varumärkesmodellen i Video Indexer](./media/customize-brand-model/customize-brand-model.png)

4. Kontrollera alternativet **Visa varumärken som föreslås av Bing** om du vill att Video Indexer ska identifiera varumärken som föreslås av Bing – lämna alternativet omarkerat om du inte gör det.

## <a name="include-brands-in-the-model"></a>Inkludera varumärken i modellen

Avsnittet **Inkludera varumärken** representerar anpassade varumärken som du vill att Video Indexer ska identifiera, även om de inte föreslås av Bing.  

### <a name="add-a-brand-to-include-list"></a>Lägga till ett varumärke som ska inkluderas i listan

1. Välj **+ Lägg till varumärke**.

    ![Anpassa varumärkesmodellen i Video Indexer](./media/customize-brand-model/add-brand.png)

    Ange ett namn (obligatoriskt), kategori (valfritt), beskrivning (valfritt) och referens-URL (valfritt).
    Kategorifältet är avsett att hjälpa dig att tagga dina varumärken. Det här fältet visas som märkets *taggar* när du använder VIDEO Indexer API:er. Varumärket "Azure" kan till exempel taggas eller kategoriseras som "Cloud".

    Referensadressfältet kan vara vilken referenswebbplats som helst för varumärket (som en länk till wikipediasidan).

2. Välj **Lägg till varumärke** så ser du att varumärket har lagts till i listan Inkludera **varumärken.**

### <a name="edit-a-brand-on-the-include-list"></a>Redigera ett varumärke i include-listan

1. Välj pennikonen bredvid det märke som du vill redigera.

    Du kan uppdatera ett varumärkes kategori, beskrivning eller referensadress. Du kan inte ändra namnet på ett varumärke eftersom varumärkenas namn är unika. Om du behöver ändra varumärket tar du bort hela varumärket (se nästa avsnitt) och skapar ett nytt varumärke med det nya namnet.

2. Välj knappen **Uppdatera** om du vill uppdatera varumärket med den nya informationen.

### <a name="delete-a-brand-on-the-include-list"></a>Ta bort ett varumärke i include-listan

1. Markera papperskorgen bredvid det märke som du vill ta bort.
2. Välj **Ta bort** och varumärket visas inte längre i listan Inkludera *varumärken.*

## <a name="exclude-brands-from-the-model"></a>Exkludera varumärken från modellen

Avsnittet **Exkludera varumärken** representerar de varumärken som du inte vill att Video Indexer ska identifiera.

### <a name="add-a-brand-to-exclude-list"></a>Lägga till ett varumärke för att utesluta lista

1. Välj **+ Lägg till varumärke.**

    Ange ett namn (obligatoriskt), kategori (valfritt).

2. Välj **Lägg till varumärke** så ser du att varumärket har lagts till i listan Uteslut *varumärken.*

### <a name="edit-a-brand-on-the-exclude-list"></a>Redigera ett varumärke i undantagslistan

1. Välj pennikonen bredvid det märke som du vill redigera.

    Du kan bara uppdatera kategorin för ett varumärke. Du kan inte ändra namnet på ett varumärke eftersom varumärkenas namn är unika. Om du behöver ändra varumärket tar du bort hela varumärket (se nästa avsnitt) och skapar ett nytt varumärke med det nya namnet.

2. Välj knappen **Uppdatera** om du vill uppdatera varumärket med den nya informationen.

### <a name="delete-a-brand-on-the-exclude-list"></a>Ta bort ett varumärke i undantagslistan

1. Markera papperskorgen bredvid det märke som du vill ta bort.
2. Välj **Ta bort** och varumärket visas inte längre i listan Uteslut *varumärken.*

## <a name="next-steps"></a>Nästa steg

[Anpassa brands-modellen med API:er](customize-brands-model-with-api.md)
