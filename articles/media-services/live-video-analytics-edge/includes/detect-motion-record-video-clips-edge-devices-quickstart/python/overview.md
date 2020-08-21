---
ms.openlocfilehash: 768e79c6a2471715b336f90748ad97ecfcc4bbc2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682311"
---

![översikt](../../../media/quickstarts/overview-qs4.png)

Föregående diagram visar hur signal flödet i den här snabb starten. [En Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real Time Streaming Protocol). En [RTSP-källmapp](../../../media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video bild rutor till noden för [avkänning av rörelser](../../../media-graph-concept.md#motion-detection-processor) . RTSP-källan skickar samma video bild rutor till en [signal grind processor](../../../media-graph-concept.md#signal-gate-processor) , som förblir stängd tills den utlöses av en händelse.

När processen för rörelse identifiering identifierar rörelse i videon, skickar den en händelse till signal porten processor, vilket utlöser den. Porten öppnas under den konfigurerade tiden och skickar video ramar till [filen Sink](../../../media-graph-concept.md#file-sink) -noden. Den här Sink-noden registrerar videon som en MP4-fil på din gräns enhets lokala fil system. Filen sparas på den konfigurerade platsen.

I den här snabb starten kommer du att:

1. Skapa och distribuera medie diagrammet.
1. Tolka resultatet.
1. Rensa resurser.
