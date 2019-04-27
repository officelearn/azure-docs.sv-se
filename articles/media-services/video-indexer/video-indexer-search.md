---
title: Hitta speciella ögonblick i videor – Video Indexer
titlesuffix: Azure Media Services
description: Det här avsnittet visar hur du hitta speciella ögonblick i videor med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: 07b3c806dc5df5f93bee3206cbca53485675e7dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560373"
---
# <a name="find-exact-moments-within-videos"></a>Hitta exakta ögonblick i videor

Det här avsnittet visar sökalternativ som hjälper dig att hitta speciella ögonblick i videor.

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Söka bland alla videor i ditt konto.

    I följande exempel söker vi du efter alla videor som pratar om säkerhet och i vilket Satya visas

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
3. Sök sammanfattade insikter om videon.

    Du kan sedan söka i en video genom att klicka på **spela upp** för videon. Sedan kan du söka i videon genom att välja den **Search** fliken. 

    I följande exempel söker vi du efter ”skydda” i den markerade videon.

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    Om du klickar på ett av resultaten ger spelaren du till den tidpunkten i videon. Du kan uppnå player/bra överblick och synkronisering i ditt program. Mer information finns i [bädda in Video Indexer widgetar i ditt program](video-indexer-embed-widgets.md). 
4. Sök specificering av videon.
    
    Om du vill skapa egna clip baserat på videon som du hittat och tryck på den **redigera** knappen. Den här sidan visar video tillsammans med dess insikter som filter. Mer information finns i [visa och redigera videoindexering](video-indexer-view-edit.md). 

    Du kan söka i videon för att visa endast de rader som du är intresserad av och använda insikterna som sida för att filtrera de delar som du vill se. När du är klar kan du förhandsgranska din clip och trycka på **publicera** att skapa det nya klippet som visas i galleriet.
    
    I följande exempel sökte vi texten ”Mixad verklighet”. Vi kan även tillämpas ytterligare filter som visas på skärmen nedan.
    
    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Nästa steg 

När du har hittat på videon som du vill arbeta med kan du fortsätta att bearbeta videon, enligt beskrivningen i något av följande avsnitt: 

- [Skapa ny videoinsikter baserat på befintlig video](video-indexer-create-new.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Se också

[Översikt över Video Indexer](video-indexer-overview.md)
