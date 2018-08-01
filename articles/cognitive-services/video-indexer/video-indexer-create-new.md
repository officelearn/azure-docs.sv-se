---
title: Använd Azure Video Indexer för att skapa videoanalyser av befintliga videor | Microsoft Docs
description: Det här avsnittet visar hur du skapar och publicerar videoinsikter baserat på vissa andra video.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 161a47f72a0f8038a515c09f25ec2a8e8a520547
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390848"
---
# <a name="create-highlights-from-existing-videos"></a>Skapa höjdpunkter från befintliga videor

Det här avsnittet visar hur du skapar och publicerar videoinsikter baserat på vissa andra video.

1. Logga in på din [Video Indexer](https://api-portal.videoindexer.ai/) konto.
2. Hitta en video som du vill skapa din videoinsikter.
3. Tryck på **spela upp**.

    På sidan visas videons sammanfattade insikter. 

    ![Insikter](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Tryck på den **redigera** knappen.

    Den här sidan visar fullständig uppdelning av en video. En analys på detaljnivå är uppdelat i block. Adressblock är här för att göra det enklare att gå igenom data. Till exempel kan blockera delas upp baserat på när talare ändra eller så finns det en paus. Du kan skapa din egen spelningslista som bara innehåller rader som du vill. För att visa endast vissa delar av källan video, kan du filtrera efter ämnen/nyckelord, sentiment, personer, talare. Du kan välja att bara visa videons avskriften eller OCR.    

    ![Insikter](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Skapa din spellista.

    Om du vill lägga till eller ta bort rader till och från din spellista, trycker du på **+** / **-**.

5. Förhandsgranska din spellista.

    När du är klar skapar spelningslistan, tryck på **förhandsversion**.
6. Publicera listan.

    När du förhandsgranskar spelningslistan, kan du publicera den.

    När du publicerar listan, läggs den till i listan över dina videoinsikter.


## <a name="next-steps"></a>Nästa steg 

När du har skapat den nya spelningslistan kan du fortsätta att bearbeta den, enligt beskrivningen i något av följande avsnitt: 

- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Se också

[Video Indexer-översikt](video-indexer-overview.md) 
