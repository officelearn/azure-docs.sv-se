---
title: Fel koder för Azure Media Services Live-händelse | Microsoft Docs
description: Den här artikeln innehåller fel koder för Live-händelser.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599475"
---
# <a name="media-services-live-event-error-codes"></a>Fel koder för Media Services Live-händelse

I tabellen nedan visas fel koder för [Live-händelser](live-events-outputs-concept.md) :

|Fel|Beskrivning|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Det här felet uppstår när inkommande kodare skickar strömmar som överskrider 30fps för kodning av Live-händelser/-kanaler.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Det här felet uppstår när inkommande kodare skickar strömmar som överskrider följande lösningar: 1920x1088 för att koda Live-händelser/-kanaler och 4096 x 2160 för direkt sändnings händelser/kanaler.|

## <a name="see-also"></a>Se även

[Felkoder för strömnings slut punkt (ursprung)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Nästa steg

[Självstudie: strömma live med Media Services](stream-live-tutorial-with-api.md)
