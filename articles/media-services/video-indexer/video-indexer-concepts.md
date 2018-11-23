---
title: Video Indexer-begrepp
titlesuffix: Azure Media Services
description: Det här avsnittet beskrivs några koncept för tjänsten Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: fde98dac45eeda9ec2c842b9d8e0f93134ab8092
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292464"
---
# <a name="video-indexer-concepts"></a>Video Indexer-begrepp
 
Den här artikeln beskriver några koncept för tjänsten Video Indexer.
    
## <a name="summarized-insights"></a>Sammanfattande insikter

Sammanfattande insights innehåller en sammansatt vy av data: ytor, ämnen, känslor. I stället för att gå över var och en av tusentals tidsintervall och kontrollera vilka ansiktena är i den, till exempel sammanfattade insights innehåller alla ansikten och för vart och ett tidsintervall som det visas i och % av tiden visas.

## <a name="time-range-vs-adjusted-time-range"></a>tidsintervallet jämfört med justerade tidsintervall

TimeRange är tidsintervallet i den ursprungliga videon. AdjustedTimeRange är tidsintervallet i förhållande till den aktuella spellistan. Eftersom du kan skapa en spellista från olika rader med olika videor, kan du ta en video för 1 timme och använder bara 1 rad från den, till exempel 10:00-10:15. I så fall kan du har en spellista med 1 rad där tidsintervallet är 10:00-10:15 men adjustedTimeRange är 00:00-00:15.
 
## <a name="blocks"></a>block

Block är avsedda att göra det enklare att gå igenom data. Blocken kan till exempel delas in baserat på när talare ändras eller det förekommer en lång paus.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [hur du registrerar dig och ladda upp din första video](video-indexer-get-started.md).

## <a name="see-also"></a>Se också

[Översikt över Video Indexer](video-indexer-overview.md)
