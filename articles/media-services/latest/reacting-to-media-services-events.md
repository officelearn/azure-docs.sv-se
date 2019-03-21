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
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850912"
---
# <a name="handling-event-grid-events"></a>Hantera Event Grid-händelser

Media Services-händelser tillåta program att reagera på olika händelser (till exempel jobb statusändringshändelsen) med hjälp av moderna arkitekturer utan server. Detta sker utan behov av komplicerade kod eller dyrt och ineffektiv avsökningen tjänster. I stället händelser skickas [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till händelsehanterare som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), eller till och med dina egna Webhook och du endast betala för det du använder. Information om priser finns i [priser för Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Tillgänglighet för Media Services-händelser är knuten till Event Grid [tillgänglighet](../../event-grid/overview.md) och blir tillgängliga i andra regioner som Event Grid.  

## <a name="media-services-events-and-schemas"></a>Media Services-händelser och scheman

Händelserutnät använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) händelsen skicka meddelanden till prenumeranter. Media Services-händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Media Services-händelse eftersom egenskapen händelsetyp börjar med ”Microsoft.Media”.

Mer information finns i [Media Services Händelsescheman](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Metoder för att använda händelser

Program som hanterar Media Services-händelser bör följa några rekommenderade metoder:

* När flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, är det viktigt inte att anta händelser är från en viss källa, utan att kontrollera ämne för meddelandet att se till att det kommer från storage-konto som du förväntade dig.
* Kontrollera dessutom att händelsetyp är något du är beredd på att processen och förutsätter inte att alla händelser som visas är de typer som du förväntar dig.
* Ignorera fält som du inte förstår.  Den här metoden hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.
* Använda ”ämne” prefix och suffix matchar för att begränsa händelser till en viss händelse.

## <a name="next-steps"></a>Nästa steg

[Hämta händelser för jobbet](job-state-events-cli-how-to.md)
