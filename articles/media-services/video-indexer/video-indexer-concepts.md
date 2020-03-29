---
title: Begrepp för videoindexerare
titleSuffix: Azure Media Services
description: I den här artikeln beskrivs några begrepp i Azure Media Services Video Indexer-tjänsten.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900678"
---
# <a name="video-indexer-concepts"></a>Begrepp för videoindexerare
 
I den här artikeln beskrivs några begrepp i tjänsten Video Indexer.
    
## <a name="summarized-insights"></a>Sammanfattade insikter

Sammanfattade insikter innehåller en aggregerad vy av data: ansikten, ämnen, känslor. I stället för att till exempel gå över vart och ett av de tusentals tidsintervallen och kontrollera vilka ansikten som finns i den, innehåller de sammanfattade insikterna alla ansikten och för var och en, de tidsintervall som visas i och % av tiden visas.

## <a name="time-range-vs-adjusted-time-range"></a>tidsintervall kontra justerat tidsintervall

TimeRange är tidsintervallet i den ursprungliga videon. AdjustedTimeRange är tidsintervallet i förhållande till den aktuella spellistan. Eftersom du kan skapa en spellista från olika rader med olika videor kan du ta en 1-timmarsvideo och bara använda en rad från den, till exempel 10:00-10:15. I så fall har du en spellista med 1 rad, där tidsintervallet är 10:00-10:15 men den justeradeTimeRange är 00:00-00:15.
 
## <a name="blocks"></a>Block

Block är avsedda att göra det lättare att gå igenom data. Blocken kan till exempel delas in baserat på när talare ändras eller det förekommer en lång paus.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [Så här registrerar du dig och laddar upp din första video](video-indexer-get-started.md).

## <a name="see-also"></a>Se även

[Översikt över Video Indexer](video-indexer-overview.md)
