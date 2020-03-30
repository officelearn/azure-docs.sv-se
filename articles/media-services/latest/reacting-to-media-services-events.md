---
title: Reagera på Azure Media Services-händelser | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder Azure Event Grid för att prenumerera på Media Services-händelser.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: e24bacb0ea7ab406442022915872fc77e9cc1a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887892"
---
# <a name="handling-event-grid-events"></a>Hantera Event Grid-händelser

Media Services-händelser gör det möjligt för program att reagera på olika händelser (till exempel händelsen för ändring av jobbtillstånd) med hjälp av moderna serverlösa arkitekturer. Det gör den utan behov av komplicerad kod eller dyra och ineffektiva valtjänster. I stället trycks händelser via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till händelsehanterare som Azure [Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med till din egen Webhook, och du betalar bara för det du använder. Information om priser finns i [Prissättning för Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Tillgänglighet för Media Services-händelser är knuten till [event grid-tillgänglighet](../../event-grid/overview.md) och blir tillgänglig i andra regioner som Event Grid gör.  

## <a name="media-services-events-and-schemas"></a>Evenemang och scheman för Media Services

Event grid använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) för att dirigera händelsemeddelanden till prenumeranter. Media Services-händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Media Services-händelse eftersom eventType-egenskapen börjar med "Microsoft.Media".

Mer information finns i [Media Services-händelsescheman](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser

Program som hanterar Media Services-händelser bör följa några rekommenderade metoder:

* Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare är det viktigt att inte anta att händelser kommer från en viss källa, utan för att kontrollera ämnet för meddelandet för att säkerställa att det kommer från det lagringskonto du förväntar dig.
* På samma sätt kontrollerar du att eventType är en som du är beredd att bearbeta och förutsätter inte att alla händelser du får kommer att vara de typer du förväntar dig.
* Ignorera fält som du inte förstår.  Den här metoden hjälper dig att hålla dig motståndskraftig mot nya funktioner som kan läggas till i framtiden.
* Använd prefixet "subject" och suffixmatchningar för att begränsa händelser till en viss händelse.

> [!NOTE]
> Händelser omfattas av servicenivåavtalet för händelsenät [(SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Om du vill få händelseaviseringar med API:er kan du se exempel på hur du använder händelser med [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) eller [Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Nästa steg

* [Övervaka händelser - portal](monitor-events-portal-how-to.md)
* [Övervaka händelser – CLI](job-state-events-cli-how-to.md)
