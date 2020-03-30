---
title: Anpassa en varumärkesmodell i Video Indexer - Azure
titleSuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad som är en Brands-modell i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: ca3d825fb2f4184448cc279d9408f47ad4ad004a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838363"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Anpassa en varumärkesmodell i Video Indexer

Video Indexer stöder varumärkesidentifiering från tal- och visuell text vid indexering och omindexering av video- och ljudinnehåll. Varumärkesidentifieringsfunktionen identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bings varumärkesdatabas. Om Microsoft till exempel nämns i ett video- eller ljudinnehåll eller om det visas i visuell text i en video, identifierar Video Indexer det som ett varumärke i innehållet. Varumärken är disambiguated från andra termer med hjälp av sammanhang.

Varumärkesidentifiering är användbart i en mängd olika affärsscenarier som innehållsarkiv och upptäckt, kontextuell annonsering, analys av sociala medier, detaljhandelskontyss analys och många fler. Video Indexer varumärkesidentifiering kan du indexera varumärkesomnämnanden i tal och visuell text, med hjälp av Bings varumärkesdatabas samt med anpassning genom att skapa en anpassad Brands-modell för varje Video Indexer-konto. Den anpassade Brands modell funktionen kan du välja om Video Indexer kommer att upptäcka varumärken från Bing varumärken databas, utesluta vissa märken från att upptäckas (i huvudsak skapa en svart lista över varumärken), och inkludera varumärken som bör vara en del av din modell som kanske inte finns i Bings varumärkesdatabas (i huvudsak skapar en vit lista över varumärken). Den anpassade brands-modellen som du skapar är bara tillgänglig i kontot där du skapade modellen.

## <a name="out-of-the-box-detection-example"></a>Ut ur rutan identifiering exempel

I presentationen [av Microsoft Build 2017 Dag 2](https://www.videoindexer.ai/media/ed6ede78ad/) visas varumärket "Microsoft Windows" flera gånger. Ibland i avskriften, ibland som visuell text och aldrig som ordagrant. Video Indexer upptäcker med hög precision att en term verkligen är varumärke baserat på sammanhanget, som täcker över 90k varumärken ur lådan, och ständigt uppdatera. Klockan 02:25 upptäcker Video Indexer varumärket från tal och sedan igen klockan 02:40 från visuell text, som är en del av Windows-logotypen.

![Översikt över varumärken](./media/content-model-customization/brands-overview.png)

Talar om fönster i samband med konstruktion kommer inte att upptäcka ordet "Windows" som ett varumärke, och samma för Box, Apple, Fox, etc., baserat på avancerade Machine Learning algoritmer som vet hur man disambiguate från sammanhang. Varumärkesidentifiering fungerar för alla våra språk som stöds. Klicka här för [fullständig Microsoft Build 2017 Dag 2 keynote video och index](https://www.videoindexer.ai/media/ed6ede78ad/).

För att ta med egna varumärken, kolla in Nästa steg.

## <a name="next-steps"></a>Nästa steg

[Anpassa brands-modellen med API:er](customize-brands-model-with-api.md)

[Anpassa Brands-modellen med hjälp av webbplatsen](customize-brands-model-with-website.md)
