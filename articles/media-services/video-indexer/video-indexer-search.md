---
title: Sök efter exakt moment i videor med Video Indexer
titleSuffix: Azure Media Services
description: Lär dig hur du söker efter exakta moment i videor med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 1c3326456fefbbef9228cf26b5821f306cd21601
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127914"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Sök efter exakt moment i videor med Video Indexer

Det här avsnittet visar de sökalternativ som gör att du kan söka efter exakta moment i videor.

1. Gå till [video Indexer](https://www.videoindexer.ai/) webbplats och logga in.
2. Sök bland alla videor i ditt konto.

    I följande exempel söker vi efter alla videor som talar om säkerhet och i vilken Satya visas.

    ![Sök efter video i Video Indexer](./media/video-indexer-search/video-indexer-search01.png)

3. Sök i de sammanfattande insikterna av videon.

    Du kan sedan söka i en video genom att välja **spela upp** på videon. Sedan kan du söka efter exakt en stund i videon genom att välja fliken **Sök** .

    I följande exempel söker vi efter "Secure" i den valda videon.

    ![Sök i en video med Video Indexer](./media/video-indexer-search/video-indexer-search02.png)

    Om du väljer ett av resultaten ger spelaren dig det exakta tillfället i videon. Du kan nå Player/Insights-vyn och synkroniseringen i din app. Mer information finns i [bädda in video Indexer-widgetar i din app](video-indexer-embed-widgets.md).

4. Sök igenom den detaljerade indelningen av videon.

    Om du vill skapa ett eget klipp baserat på videon som du hittat väljer du knappen **Redigera** . På den här sidan visas videon tillsammans med dess insikter som filter. Mer information finns i [Visa och redigera video Indexer insikter](video-indexer-view-edit.md).

    Du kan söka efter exakt en stund i videon för att bara visa de rader som du är intresse rad av. Använd sido insikter för att filtrera de delar som du vill se. När du är klar kan du förhandsgranska ditt klipp och välja **publicera** för att skapa det nya klippet som visas i galleriet.

    I följande exempel sökte vi efter texten "Mixad verklighet". Vi har också använt ytterligare filter, som du ser på skärmen nedan.

    ![Sök efter exakta moment i Video Indexer](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Nästa steg

När du har hittat den exakta tidpunkten i videon som du vill arbeta med kan du fortsätta att bearbeta videon. Mer information finns i följande avsnitt:

- [Använd dina videor med djupgående insikter](use-editor-create-project.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Se även

[Översikt över Video Indexer](video-indexer-overview.md)
