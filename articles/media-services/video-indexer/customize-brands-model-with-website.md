---
title: Använda Video Indexer-webbplats för att anpassa en varumärken modell – Azure
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en varumärken modell med Video Indexer-webbplatsen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 2522ede85c290fa238c0d5a5604d2dfbab984cdc
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285293"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Anpassa en varumärken modell med Video Indexer-webbplats

Video Indexer stöder varumärke identifiering från tal- och visual text under indexering och omindexering av video-och ljudinnehåll. Funktionen för identifiering av varumärke identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bings varumärken databas. Till exempel identifierar om Microsoft nämns i en video- eller ljudinnehåll innehåll eller om den visas i visual text i en video, Video Indexer den som ett varumärke i innehållet. En anpassad varumärken modell kan du välja huruvida du Video Indexer identifierar varumärken från Bing varumärken databas, undanta vissa varumärken inte har identifierats (skapa en lista med blockerade användare av i stort sett) och inkludera varumärken som ska ingå i din modell som kanske inte är i Bings varumärken databasen (skapa en lista för tillåten av i stort sett).

En detaljerad översikt finns i [översikt](customize-brands-model-overview.md).

Du kan använda Video Indexer-webbplatsen för att skapa, använda och redigera anpassade varumärken modeller har identifierats i en video, enligt beskrivningen i det här avsnittet. Du kan också använda API: et, enligt beskrivningen i [anpassa varumärken modellen med hjälp av API: er](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Redigera inställningar av modellen varumärken  

Du har möjlighet att ange om du vill att varumärken från Bing varumärken databasen ska identifieras. För detta måste du redigera inställningarna för din modell varumärken.

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Om du vill anpassa en modell i ditt konto, klickar du på den **innehåll modellanpassning** knappen i det övre högra hörnet på sidan.
 
   ![Anpassa innehållsmodellen](./media/content-model-customization/content-model-customization.png) 
3. Om du vill redigera varumärken, Välj den **varumärken** fliken.

    ![Anpassa varumärken modell](./media/customize-brand-model/customize-brand-model.png)
4. Kontrollera den **visa varumärken som föreslås av Bing** alternativ, om du vill använda för Video Indexer att inkludera varumärken som föreslås av Bing. Lämna alternativet är avmarkerat om du inte vill att Video Indexer för att identifiera varumärken som föreslås av Bing i ditt innehåll. 

## <a name="include-brands-in-the-model"></a>Inkludera varumärken i modellen

Den **inkluderar varumärken** avsnittet representerar anpassade varumärken som du vill använda för Video Indexer att identifiera även om de inte föreslås av Bing.  

### <a name="add-a-brand"></a>Lägg till ett varumärke

1. Klicka på ”+ Lägg till varumärke”.

    ![Anpassa varumärken modell](./media/customize-brand-model/add-brand.png)

    Ange ett namn (krävs), en kategori (valfritt), en beskrivning (valfritt) och referera URL (valfritt).
    Kategorifältet är avsedd att hjälpa dig tagga dina varumärken. Det här fältet visas som varumärken *taggar* när du använder API: er för Video Indexer. Varumärket ”Azure” kan till exempel taggade eller kategoriserade som ”moln”.

    Referens för URL-fält kan vara någon referens-webbplats för varumärke, till exempel en länk till dess Wikipedia-sida.
2. Klicka på ”Lägg till varumärke” och du ser att varumärket har lagts till i **inkluderar varumärken** lista.

### <a name="edit-a-brand"></a>Redigera ett varumärke

1. Klicka på pennikonen bredvid varumärket som du vill redigera.

    Du kan uppdatera kategori, beskrivning och URL: en för referens för ett varumärke. Du kan inte ändra namnet på ett varumärke eftersom namnen på varumärken är unika. Om du vill ändra namnet, ta bort hela varumärket (se nästa avsnitt) och skapa ett nytt varumärke med det nya namnet.
2. Klicka på den **uppdatera** knappen för att uppdatera varumärket med den nya informationen.

### <a name="delete-a-brand"></a>Ta bort ett varumärke

1. Klicka på Papperskorgen bredvid varumärket som du vill ta bort.
2. Klicka på ”Ta bort” och varumärket inte längre visas i din *inkluderar varumärken* lista.

## <a name="exclude-brands-from-the-model"></a>Exkludera varumärken från modellen

Den **undanta varumärken** avsnittet representerar de varumärken som du vill använda för Video Indexer inte att identifiera.

### <a name="add-a-brand"></a>Lägg till ett varumärke

1. Klicka på ”+ Lägg till varumärke”.

    Ange ett namn (obligatoriskt), kategori (valfritt).
2. Klicka på ”Lägg till varumärke” och du ser att varumärket har lagts till i *undanta varumärken* lista.

### <a name="edit-a-brand"></a>Redigera ett varumärke

1. Klicka på pennikonen bredvid varumärket som du vill redigera.

    Du kan bara uppdatera kategorin för ett varumärke. Du kan inte ändra namnet på ett varumärke eftersom namnen på varumärken är unika. Om du vill ändra namnet, ta bort hela varumärket (se nästa avsnitt) och skapa ett nytt varumärke med det nya namnet.
2. Klicka på den **uppdatera** knappen för att uppdatera varumärket med den nya informationen.

### <a name="delete-a-brand"></a>Ta bort ett varumärke

1. Klicka på Papperskorgen bredvid varumärket som du vill ta bort.
2. Klicka på ”Ta bort” och varumärket inte längre visas i din *undanta varumärken* lista.

## <a name="next-steps"></a>Nästa steg

[Anpassa varumärken modell med API: er](customize-brands-model-with-api.md)
