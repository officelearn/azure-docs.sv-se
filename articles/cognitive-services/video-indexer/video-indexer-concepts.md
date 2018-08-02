---
title: Azure Video Indexer-begrepp | Microsoft Docs
description: Det här avsnittet beskrivs några koncept för tjänsten Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399503"
---
# <a name="video-indexer-concepts"></a>Video Indexer-begrepp
 
Det här avsnittet beskrivs några koncept för tjänsten Video Indexer.
    
## <a name="summarized-insights"></a>Sammanfattande insikter

Sammanfattande insights innehåller en sammansatt vy av data: ytor, nyckelord, sentiment. I stället för att gå över var och en av tusentals tidsintervall och kontrollera vilka ansiktena är i den, till exempel sammanfattade insights innehåller alla ansikten och för vart och ett tidsintervall som det visas i och % av tiden visas.

## <a name="topicskeywords"></a>Avsnitt om/nyckelord

Avsnitt om/nyckelord är i listan över viktiga fraser som Video Indexer extraherar i texten. Exempelvis en video med Scott Guthrie kan innehålla följande avsnitt/nyckelord: säkerhet, Azure, Microsoft Cloud, intäkter.

## <a name="sentiments"></a>sentiment

När Video Indexer analyserar avskrifter, identifierar sentiment samt. Exempel: ”det här är en väldigt spännande händelse” är en positiv attityd.

## <a name="time-range-vs-adjusted-time-range"></a>tidsintervallet jämfört med justerade tidsintervall

TimeRange är tidsintervallet i den ursprungliga videon. AdjustedTimeRange är tidsintervallet i förhållande till den aktuella spellistan. Eftersom du kan skapa en spellista från olika rader med olika videor, kan du ta en video för 1 timme och använder bara 1 rad från den, till exempel 10:00-10:15. I så fall kan du har en spellista med 1 rad där tidsintervallet är 10:00-10:15 men adjustedTimeRange är 00:00-00:15.
 
## <a name="blocks"></a>block

Block är avsedda att göra det enklare att gå igenom data. Till exempel kan blockera delas upp baserat på när talare ändra eller så finns det en paus.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [hur du registrerar dig och ladda upp din första video](video-indexer-get-started.md).

## <a name="see-also"></a>Se också

[Video Indexer-översikt](video-indexer-overview.md)
