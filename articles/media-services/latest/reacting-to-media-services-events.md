---
title: Reagera på Azure Media Services händelser | Microsoft Docs
description: Använda Azure händelse rutnätet för att prenumerera på Media Services-händelser.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 969957d53824bd70440e5529b83bc830bb5d9cc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="reacting-to-media-services-events"></a>Reagera på Media Services-händelser

Media Services händelser kan program reagerar på olika händelser (till exempel jobbet statusändringshändelsen) med moderna serverlösa arkitekturerna. Detta sker utan att behöva komplicerad kod eller dyrt och ineffektiv avsökning tjänster. I stället händelser skickas [Azure händelse rutnätet](https://azure.microsoft.com/services/event-grid/) till händelsehanterare som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logikappar](https://azure.microsoft.com/services/logic-apps/), eller till och med din egen Webhook, och du endast betala för vad du använder. Information om priser finns i [händelse rutnätet priser](https://azure.microsoft.com/pricing/details/event-grid/).

Tillgänglighet för Media Services-händelser som är knutna till händelse rutnätet [tillgänglighet](../../event-grid/overview.md) och ska vara tillgänglig i andra regioner som händelsen rutnätet.  

## <a name="available-media-services-events"></a>Tillgängliga Media Services-händelser

Används av rutnätet händelse [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) händelse meddelanden till prenumeranter.  Media Services händelseprenumerationer kan för närvarande är följande Händelsetyp:  

|Händelsenamnet|Beskrivning|
|----------|-----------|
| Microsoft.Media.JobStateChange| Utlöses när ett tillstånd för jobb ändringarna. |

## <a name="event-schema"></a>Händelseschema

Media Services-händelser innehåller all information som du måste svara för ändringar i dina data.  Du kan identifiera en händelse för Media Services eftersom egenskapen eventType börjar med ”Microsoft.Media”.

Mer information finns i [Media Services händelse scheman](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Metoder för förbrukning av händelser

Program som hanterar händelser för Media Services bör följa några rekommenderade metoder:

* Flera prenumerationer kan konfigureras för vägen händelser till samma händelsehanterare, är det viktigt inte kan ta över händelser som kommer från en viss källa, men för att kontrollera avsnittet i meddelandet för att se till att det kommer från lagringskontot som du förväntar dig.
* Kontrollera dessutom att eventType är som du är beredd på att processen och inte förutsätter att alla händelser som visas är de typer som du förväntar dig.
* Ignorera fält som du inte förstår.  Den här övningen kommer att skydda dig flexibel till nya funktioner som kan läggas till i framtiden.
* Använd ”ämne” prefix och suffix matchar för att begränsa händelser till en viss händelse.

## <a name="next-steps"></a>Nästa steg

[Hämta händelser från jobb](job-state-events-cli-how-to.md)
