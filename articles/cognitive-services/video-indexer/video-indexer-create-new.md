---
title: Använd Azure Video Indexeringsfunktion för att skapa insikter om video från befintliga videor | Microsoft Docs
description: Det här avsnittet visar hur du skapar och publicerar video analyser baserade på vissa andra video.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 06/19/2018
ms.author: juliako
ms.openlocfilehash: 8b336f2818769af8372320cadc20e3a152e51de2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265588"
---
# <a name="create-highlights-from-existing-videos"></a>Skapa markeringar från befintliga videor

Det här avsnittet visar hur du skapar och publicerar video analyser baserade på vissa andra video.

1. Logga in på ditt [Video indexeraren](https://api-portal.videoindexer.ai/) konto.
2. Hitta en video som du vill skapa din video insikter.
3. Tryck på **spela upp**.

    På sidan visas på video sammanfattade insikter. 

    ![Insikter](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Tryck på den **redigera** knappen.

    Den här sidan visar fullständig uppdelning av en video. Fördelningen är uppdelad i block. Block finns här för att göra det lättare att gå igenom informationen. Till exempel kan block delas upp baserat på när högtalare ändrar eller det finns en paus. Du kan skapa din egen spelningslista som bara innehåller rader som du vill. Du kan filtrera efter artiklar/nyckelord, våra, personer, högtalare så att bara vissa delar av källan video. Du kan välja att bara visa videons betyg eller OCR.    

    ![Insikter](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Skapa din spelningslista.

    Om du vill lägga till eller ta bort rader till/från din spelningslista, trycker du på **+** / **-**.

5. Förhandsgranska din spelningslista.

    När du är klar att skapa listan trycker du på **Preview**.
6. Publicera listan.

    Du kan publicera när du förhandsgranska listan.

    När du publicerar listan, läggs den till listan över dina video insikter.


## <a name="next-steps"></a>Nästa steg 

När du skapar nya spelningslista kan kan du fortsätta att bearbeta, enligt beskrivningen i något av följande avsnitt: 

- [Bearbeta innehåll med Video indexeraren REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Se också

[Indexeraren videoöversikt](video-indexer-overview.md) 
