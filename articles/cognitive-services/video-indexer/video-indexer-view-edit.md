---
title: Visa och redigera Video Indexer-insikter
titlesuffix: Azure Cognitive Services
description: Det här avsnittet visar hur du visa och redigera videoindexering.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: c9b229e2fb3297d724ec8de02bf54e9765689ab7
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984545"
---
# <a name="view-and-edit-video-indexer-insights"></a>Visa och redigera Video Indexer-insikter

Det här avsnittet visar hur du visa och redigera Video Indexer insikter om en video.

1. Bläddra till den [Video Indexer](https://www.videoindexer.ai/) och logga in.
2. Hitta en video som du vill skapa dina Video Indexer insikter. Mer information finns i [hitta speciella ögonblick i videor](video-indexer-search.md).
3. Tryck på **spela upp**.

    På sidan visas videons sammanfattade insikter. 

    ![Insikter](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. Visa de sammanfattade insikterna för videon. 

    Sammanfattande insights visar en sammansatt vy av data: ytor, nyckelord, sentiment. Du kan till exempel se ansikten för personer och tidsintervall som varje ansikte visas i och % av tiden som det visas.

    Spelaren och insikterna som är synkroniserade. Till exempel om du klickar på ett nyckelord eller raden avskrift ger spelaren du till den tidpunkten i videon. Du kan uppnå player/bra överblick och synkronisering i ditt program. Mer information finns i [bädda in Azure Indexer widgetar i ditt program](video-indexer-embed-widgets.md). 

3. Redigera Video Indexer insikter.

    Tryck på Redigera under videon. Sidan som visar fullständig uppdelning av en video visas. En analys på detaljnivå är uppdelat i block. Adressblock är här för att göra det enklare att gå igenom data. Till exempel kan blockera delas upp baserat på när talare ändra eller så finns det en paus. Du kan skapa din egen spelningslista som bara innehåller rader som du vill. För att visa endast vissa delar av källan video, kan du filtrera efter ämnen/nyckelord, sentiment, personer, talare. Du kan välja att bara visa videons avskriften eller OCR.  

    ![Insikter](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du skapar en egen Video Indexer insights baserat på vissa andra video](video-indexer-create-new.md).

## <a name="see-also"></a>Se också

[Video Indexer-översikt](video-indexer-overview.md)

