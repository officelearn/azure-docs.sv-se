---
title: Skapa videoanalyser av befintliga videor – Azure
titlesuffix: Azure Media Services
description: Det här avsnittet visar hur du skapar och publicerar videoinsikter baserat på befintliga videofiler.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 4a65e88e3f94f64a56bde882b535030968ae354d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560238"
---
# <a name="create-highlights-from-existing-videos"></a>Skapa höjdpunkter från befintliga videor

Det här avsnittet visar hur du skapar och publicerar videoinsikter baserat på någon annan video.

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Leta reda på en video som du vill använda för att skapa videoinsikter.
3. Tryck på **Spela upp**.

    På sidan visas videons sammanfattade insikter. 

    ![Insikter](./media/video-indexer-create-new/video-indexer-summarized-insights.png)
3. Tryck på knappen **Redigera**.

    På den här sidan visas den fullständiga uppdelningen av en video. Den är indelad i block. Blocken gör det enklare att gå igenom dessa data. Blocken kan till exempel delas in baserat på när talare ändras eller det förekommer en lång paus. Du kan skapa en egen spellista som bara innehåller rader som du vill ha med. Om du bara vill visa specifika delar av källvideon kan du filtrera efter ämnen/nyckelord, attityder, personer och talare. Du kan välja att bara visa videons avskrift eller OCR.    

    ![Insikter](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)
4. Skapa spellistan.

    Lägg till eller ta bort rader i spellistan genom att trycka på **+**/**-**.
5. Förhandsgranska spellistan.

    När du är klar med att skapa spellistan trycker du på **Förhandsgranska**.
6. Publicera spellistan.

    När du har förhandsgranskat spellistan kan du publicera den.

    När du har publicerat spellistan läggs den till i listan över videoinsikter.


## <a name="next-steps"></a>Nästa steg 

När du har skapat den nya spellistan kan du fortsätta att bearbeta den, enligt beskrivningen i något av följande avsnitt: 

- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Se också

[Översikt över Video Indexer](video-indexer-overview.md) 
