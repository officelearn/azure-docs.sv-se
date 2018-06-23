---
title: Använd Azure Video Indexeringsfunktion för att hitta exakt stund inom videor | Microsoft Docs
description: Det här avsnittet visar hur du hittar exakt stund inom videor.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/02/2017
ms.author: juliako
ms.openlocfilehash: 75afa4efa50191466f8850d9b1313cf89a83c0a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354522"
---
# <a name="find-exact-moments-within-videos"></a>Hitta exakt stund inom videor

Det här avsnittet visar sökalternativen att hitta exakt stund inom videor.

1. Logga in på ditt [Video indexeraren](https://api-portal.videoindexer.ai/) konto.
2. Söka bland alla videoklipp i ditt konto.

    I följande exempel söka för alla videor som skapats av Channel9 med Scott Hanselman.

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
    
3. Sök sammanfattade insikter av videon.

    Du kan söka i en video genom att klicka på **spela upp** på videon. Sedan kan du söka i videon genom att välja den **Sök** fliken. Till exempel söka vi efter alla platser där ”identitetsskydd” texten används. 

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    Om du klickar på ett resultat för flyttar Windows Media player du den tidpunkt då i videon. Du kan uppnå player/insikter vy och synkronisering i ditt program. Mer information finns i [bädda in Video indexeraren widgetar i programmet](video-indexer-embed-widgets.md). 

    
4. Sök specificering av videon.

    Om du vill skapa egna fördelning baserat på videon som du hittade genom att trycka på den **redigera** knappen. Den här sidan visar fullständig uppdelning av en video. Du kan söka inom uppdelning att visa endast de rader som du är intresserad. Mer information finns i [visa och redigera Video indexeraren insikter](video-indexer-view-edit.md).

    I det här exemplet har vi sökt ”identitetsskydd” text. Vi kan också användas ytterligare filter som visas på skärmen nedan.

    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Nästa steg 

När du har hittat videon som du vill arbeta med kan du fortsätta att bearbeta video, enligt beskrivningen i något av följande avsnitt: 

- [Skapa ny video insikter baserat på befintliga video](video-indexer-create-new.md)
- [Bearbeta innehåll med Video indexeraren REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Se också

[Indexeraren videoöversikt](video-indexer-overview.md)
