---
title: Video Indexer begrepp
titleSuffix: Azure Media Services
description: I den här artikeln beskrivs några begrepp i tjänsten Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900678"
---
# <a name="video-indexer-concepts"></a>Video Indexer begrepp
 
I den här artikeln beskrivs några begrepp i Video Indexers tjänsten.
    
## <a name="summarized-insights"></a>Sammanfattande insikter

Sammanfattade insikter innehåller en sammanställd vy av data: ansikten, ämnen, känslor. I stället för att gå över varje tusentals tidsintervall och kontrol lera vilka ansikten som finns, innehåller de sammanfattande insikterna alla ansikten och för var och en, de tidsintervall som visas i och% av tiden som visas.

## <a name="time-range-vs-adjusted-time-range"></a>tidsintervall jämfört med justerat tidsintervall

TimeRange är tidsintervallet i den ursprungliga videon. AdjustedTimeRange är tidsintervallet i förhållande till den aktuella spelnings listan. Eftersom du kan skapa en spelnings lista från olika rader med olika videoklipp kan du ta en timmes video och använda bara 1 rad från den, till exempel 10:00-10:15. I så fall har du en spelnings lista med 1 rad där tidsintervallet är 10:00-10:15 men adjustedTimeRange är 00:00-00:15.
 
## <a name="blocks"></a>Delar

Block är avsedda att göra det enklare att gå igenom data. Blocken kan till exempel delas in baserat på när talare ändras eller det förekommer en lång paus.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [så här registrerar du och laddar upp din första video](video-indexer-get-started.md).

## <a name="see-also"></a>Se även

[Översikt över Video Indexer](video-indexer-overview.md)
