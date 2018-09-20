---
title: Reagera på Azure Media Services-händelser | Microsoft Docs
description: Använd Azure Event Grid prenumererar du på Media Services-händelser.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/19/2018
ms.author: juliako
ms.openlocfilehash: 143fec2ddb168b0fff0e419fa5767e9718637241
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465545"
---
# <a name="reacting-to-media-services-events"></a>Reagera på händelser för Media Services

Media Services-händelser tillåta program att reagera på olika händelser (till exempel jobb statusändringshändelsen) med hjälp av moderna arkitekturer utan server. Detta sker utan behov av komplicerade kod eller dyrt och ineffektiv avsökningen tjänster. I stället händelser skickas [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till händelsehanterare som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), eller till och med dina egna Webhook och du endast betala för det du använder. Information om priser finns i [priser för Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Tillgänglighet för Media Services-händelser är knuten till Event Grid [tillgänglighet](../../event-grid/overview.md) och blir tillgängliga i andra regioner som Event Grid.  

## <a name="available-media-services-events"></a>Tillgängliga Media Services-händelser

Händelserutnät använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) händelsen skicka meddelanden till prenumeranter.  Händelseprenumerationer för Media Services kan för närvarande kan innehålla följande händelser:  

|Händelsenamn|Beskrivning|
|----------|-----------|
| Microsoft.Media.JobStateChange| Utlöses när en tillståndet för jobbet ändringarna. |
| Microsoft.Media.LiveEventConnectionRejected | Kodarens anslutningsförsöket. |
| Microsoft.Media.LiveEventEncoderConnected | Kodaren upprättar anslutningen med live-händelse. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kodaren kopplar från. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Media-servern avslutar datagruppen eftersom den är för sent eller har ett överlappande tidsstämpel (tidsstämpel av nya data-segmentet är mindre än sluttiden tidigare datagruppen). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media-servern tar emot första data-segmentet för varje spår i stream eller anslutning. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media-server identifierar ljud och video strömmar är inte synkroniserade. Använda som en varning eftersom användarupplevelsen inte påverkas. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media-server identifierar någon av de två videoflöden som kommer från externa kodaren är inte synkroniserade. Använda som en varning eftersom användarupplevelsen inte påverkas. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicerade var 20: e sekund för varje spår när direktsändningen körs. Tillhandahåller mata in hälsoöversikt. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Media-server identifierar avbrott i den inkommande kursen. |

## <a name="event-schema"></a>Händelseschema

Media Services-händelser innehåller all information du behöver för att svara på ändringar i dina data.  Du kan identifiera en Media Services-händelse eftersom egenskapen händelsetyp börjar med ”Microsoft.Media”.

Mer information finns i [Media Services Händelsescheman](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Metoder för att använda händelser

Program som hanterar Media Services-händelser bör följa några rekommenderade metoder:

* När flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, är det viktigt inte att anta händelser är från en viss källa, utan att kontrollera ämne för meddelandet att se till att det kommer från storage-konto som du förväntade dig.
* Kontrollera dessutom att händelsetyp är något du är beredd på att processen och förutsätter inte att alla händelser som visas är de typer som du förväntar dig.
* Ignorera fält som du inte förstår.  Den här metoden hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.
* Använda ”ämne” prefix och suffix matchar för att begränsa händelser till en viss händelse.

## <a name="next-steps"></a>Nästa steg

[Hämta händelser för jobbet](job-state-events-cli-how-to.md)
