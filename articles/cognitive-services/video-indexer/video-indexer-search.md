---
title: Använd Azure Video Indexer för att hitta speciella ögonblick i videor | Microsoft Docs
description: Det här avsnittet visar hur du hitta speciella ögonblick i videor.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: 72f110c0c98e6fb6ee5fca684fdc74c6986e032c
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541414"
---
# <a name="find-exact-moments-within-videos"></a>Hitta exakta ögonblick i videor

Det här avsnittet visar sökalternativ som hjälper dig att hitta speciella ögonblick i videor.

1. Bläddra till den [Video Indexer](https://www.videoindexer.ai/) och logga in.
2. Söka bland alla videor i ditt konto.

    I följande exempel sökte vi efter alla videor som skapats av Channel 9 med Scott Hanselman.

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
    
3. Sök sammanfattade insikter om videon.

    Du kan sedan söka i en video genom att klicka på **spela upp** för videon. Sedan kan du söka i videon genom att välja den **Search** fliken. Exempelvis kan sökte vi efter alla platser där texten ”identity protection” används. 

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    Om du klickar på ett av resultaten ger spelaren du till den tidpunkten i videon. Du kan uppnå player/bra överblick och synkronisering i ditt program. Mer information finns i [bädda in Video Indexer widgetar i ditt program](video-indexer-embed-widgets.md). 

    
4. Sök specificering av videon.

    Om du vill skapa en egen analys på detaljnivå baserat på videon som du hittat och tryck på den **redigera** knappen. Den här sidan visar fullständig uppdelning av en video. Du kan söka inom en analys på detaljnivå för att visa endast de rader som du är intresserad av. Mer information finns i [visa och redigera videoindexering](video-indexer-view-edit.md).

    I det här exemplet sökte vi texten ”identity protection”. Vi kan även tillämpas ytterligare filter som visas på skärmen nedan.

    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Nästa steg 

När du har hittat på videon som du vill arbeta med kan du fortsätta att bearbeta videon, enligt beskrivningen i något av följande avsnitt: 

- [Skapa ny videoinsikter baserat på befintlig video](video-indexer-create-new.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Se också

[Video Indexer-översikt](video-indexer-overview.md)
