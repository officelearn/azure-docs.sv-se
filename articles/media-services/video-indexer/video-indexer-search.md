---
title: Hitta exakt en stund i videor – Video Indexer
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du hittar en exakt stund i videor med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 8ebf7606d1a0932cc2254e14a40a2fff550fd35f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833801"
---
# <a name="find-exact-moments-within-videos"></a>Hitta exakta ögonblick i videor

Det här avsnittet visar de sökalternativ som gör att du kan hitta exakt en stund i videor.

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Sök bland alla videor i ditt konto.

    I följande exempel söker vi efter alla videor som talar om säkerheten och i vilken Satya visas.

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
3. Sök i de sammanfattande insikterna av videon.

    Du kan sedan söka i en video genom att klicka på **spela upp** på videon. Sedan kan du söka i videon genom att välja fliken **Sök** . 

    I följande exempel söker vi efter "Secure" i den valda videon.

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    Om du klickar på ett av resultaten kommer spelaren att flytta dig till det momentet i videon. Du kan nå Player/Insights-vyn och synkroniseringen i ditt program. Mer information finns i [bädda in video Indexer-widgetar i ditt program](video-indexer-embed-widgets.md). 
4. Sök igenom den detaljerade indelningen av videon.
    
    Om du vill skapa ett eget klipp baserat på videon som du hittat, trycker du på knappen **Redigera** . På den här sidan visas video tillsammans med insikter som filter. Mer information finns i [Visa och redigera video Indexer insikter](video-indexer-view-edit.md). 

    Du kan söka i videon om du bara vill visa de rader du är intresse rad av och använda sido insikter för att filtrera de delar som du vill se. När du är klar kan du förhandsgranska ditt klipp och trycka på **publicera** för att skapa det nya klippet som visas i galleriet.
    
    I följande exempel sökte vi efter texten "Mixad verklighet". Vi har också använt ytterligare filter, som du ser på skärmen nedan.
    
    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Nästa steg 

När du har hittat videon som du vill arbeta med kan du fortsätta att bearbeta videon, enligt beskrivningen i något av följande avsnitt: 

- [Använd dina videor med djupgående insikter](use-editor-create-project.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Se också

[Översikt över Video Indexer](video-indexer-overview.md)
