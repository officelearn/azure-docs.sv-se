---
title: Visa och redigera Azure videoindexering | Microsoft Docs
description: Det här avsnittet visar hur du visa och redigera videoindexering.
services: cognitive services
documentationcenter: ''
author: juliako
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 797c09d72402cfc1ee2524e7792cc1310a53fb1e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399485"
---
# <a name="view-and-edit-video-indexer-insights"></a>Visa och redigera Video Indexer-insikter

Det här avsnittet visar hur du visa och redigera Video Indexer insikter om en video.

1. Logga in på din [Video Indexer](https://api-portal.videoindexer.ai/) konto.
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

