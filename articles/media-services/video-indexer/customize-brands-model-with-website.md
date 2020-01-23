---
title: Använd Video Indexer webbplats för att anpassa modeller för varumärken – Azure
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en-modell med Video Indexer webbplats.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 956ca7af055768398392045ecf9b383d2eb1060f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513906"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Anpassa en varumärke modell med Video Indexer webbplats

Video Indexer stöder varumärkes identifiering från tal-och visuell text vid indexering och Omindexering av video-och ljud innehåll. Funktionen varumärkes identifiering identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bing-databasens varumärken. Om Microsoft till exempel nämns i ett video-eller ljud innehåll eller om det visas i visuell text i en video identifierar Video Indexer det som ett varumärke i innehållet. Med en modell med anpassade varumärken kan du välja om du Video Indexer ska identifiera varumärken från Bing-databaserna, undanta vissa varumärken från att identifieras (i grunden skapar en svart lista med varumärken) och inkluderar varumärken som ska ingå i din modell Det kanske inte finns i Bing-databasens varumärken (i grunden skapar du en vit lista över varumärken).

En detaljerad översikt finns i [Översikt](customize-brands-model-overview.md).

Du kan använda Video Indexer webbplats för att skapa, använda och redigera anpassade varumärke modeller som identifieras i en video, enligt beskrivningen i det här avsnittet. Du kan också använda API: et enligt beskrivningen i [Anpassa varumärkes-modellen med hjälp av API: er](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Redigera inställningarna för modellen varumärken  

Du har möjlighet att ange om du vill att märken från Bing-databasen ska identifieras eller inte. För det här måste du redigera inställningarna för din varumärkes-modell.

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Om du vill anpassa en modell i ditt konto klickar du på knappen **anpassning av innehålls modell** i det övre högra hörnet på sidan.
 
   ![Anpassa innehålls modell](./media/content-model-customization/content-model-customization.png) 
3. Om du vill redigera varumärken väljer du fliken **varumärken** .

    ![Anpassa varumärkes-modellen](./media/customize-brand-model/customize-brand-model.png)
4. Markera alternativet **Visa varumärken som föreslås av Bing** , om du vill att video Indexer ska innehålla varumärken som föreslås av Bing. Lämna alternativet avmarkerat om du inte vill att Video Indexer ska identifiera varumärken som föreslås av Bing i ditt innehåll. 

## <a name="include-brands-in-the-model"></a>Inkludera varumärken i modellen

Avsnittet **Inkludera varumärken** representerar anpassade varumärken som du vill ska video Indexer identifiera även om de inte föreslås av Bing.  

### <a name="add-a-brand"></a>Lägg till ett varumärke

1. Klicka på "+ Lägg till märke".

    ![Anpassa varumärkes-modellen](./media/customize-brand-model/add-brand.png)

    Ange ett namn (obligatoriskt), kategori (valfritt), beskrivning (valfritt) och referens-URL (valfritt).
    Kategori fältet är avsett att hjälpa dig att tagga dina varumärken. Det här fältet visas som märkes *taggar* när du använder video Indexer-API: er. Till exempel kan märket "Azure" märkas eller kategoriseras som "moln".

    Fältet referens-URL kan vara vilken referens webbplats som helst för varumärket, till exempel en länk till en wikipedia-sida.
2. Klicka på Lägg till märke så ser du att varumärket har lagts till i listan **med inkluderade märken** .

### <a name="edit-a-brand"></a>Redigera ett varumärke

1. Klicka på Penn ikonen bredvid det märke som du vill redigera.

    Du kan uppdatera kategori, beskrivning eller referens-URL för ett varumärke. Du kan inte ändra namnet på ett varumärke eftersom namn på varumärken är unika. Om du behöver ändra märkes namnet tar du bort hela varumärket (se nästa avsnitt) och skapar ett nytt varumärke med det nya namnet.
2. Klicka på knappen **Uppdatera** för att uppdatera varumärket med den nya informationen.

### <a name="delete-a-brand"></a>Ta bort ett varumärke

1. Klicka på pappers korgs ikonen bredvid det märke som du vill ta bort.
2. Klicka på "ta bort" och varumärket visas inte längre i listan *med inkluderade märken* .

## <a name="exclude-brands-from-the-model"></a>Uteslut varumärken från modellen

Avsnittet **exkludera varumärken** representerar de varumärken som du vill använda för video Indexer inte att identifiera.

### <a name="add-a-brand"></a>Lägg till ett varumärke

1. Klicka på "+ Lägg till märke".

    Ange ett namn (obligatoriskt), kategori (valfritt).
2. Klicka på Lägg till märke så ser du att varumärket har lagts till i listan *Uteslut varumärken* .

### <a name="edit-a-brand"></a>Redigera ett varumärke

1. Klicka på Penn ikonen bredvid det märke som du vill redigera.

    Du kan bara uppdatera kategorin för ett varumärke. Du kan inte ändra namnet på ett varumärke eftersom namn på varumärken är unika. Om du behöver ändra märkes namnet tar du bort hela varumärket (se nästa avsnitt) och skapar ett nytt varumärke med det nya namnet.
2. Klicka på knappen **Uppdatera** för att uppdatera varumärket med den nya informationen.

### <a name="delete-a-brand"></a>Ta bort ett varumärke

1. Klicka på pappers korgs ikonen bredvid det märke som du vill ta bort.
2. Klicka på "ta bort" och varumärket visas inte längre i listan med *exkluderade märken* .

## <a name="next-steps"></a>Nästa steg

[Anpassa varumärkes-modellen med API: er](customize-brands-model-with-api.md)
