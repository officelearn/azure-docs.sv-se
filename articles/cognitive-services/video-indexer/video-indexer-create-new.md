---
title: 'Självstudie: Skapa videoinsikter utifrån befintliga videor'
titlesuffix: Azure Cognitive Services
description: Det här avsnittet visar hur du skapar och publicerar videoinsikter baserat på befintliga videofiler.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: tutorial
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3a3e5c62d9eb96563d395106db62cc91a8307658
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983924"
---
# <a name="tutorial-create-highlights-from-existing-videos"></a>Självstudie: Skapa höjdpunkter utifrån befintliga videor

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

## <a name="see-also"></a>Se även

[Översikt över Video Indexer](video-indexer-overview.md) 
