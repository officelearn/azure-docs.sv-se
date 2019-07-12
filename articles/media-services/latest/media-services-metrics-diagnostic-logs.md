---
title: Övervaka Azure Media Services-mått och diagnostikloggar via Azure Monitor | Microsoft Docs
description: Den här artikeln ger en översikt över hur du övervakar Azure Media Services-mått och diagnostikloggar via Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806004"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Övervaka Media Services-mått och diagnostikloggar

[Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program utför. Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer, mått och loggar. Du kan övervaka diagnostikloggar för Media Services och skapa aviseringar och meddelanden för insamlade mått och loggar. Du kan visualisera och analysera data mått med [måttutforskaren](../../azure-monitor/platform/metrics-getting-started.md). Du kan skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och exportera dem till [Log Analytics](https://azure.microsoft.com/services/log-analytics/), eller använda tjänster med 3 part.

En detaljerad översikt finns i [Azure Monitor Metrics](../../azure-monitor/platform/data-platform.md) och [Azure Monitor diagnostiska loggar](../../azure-monitor/platform/diagnostic-logs-overview.md).

Det här avsnittet beskrivs stöds [Media Services mått](#media-services-metrics) och [Media Services diagnostiska loggar](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Media Services-mått

Mätvärden samlas in med jämna mellanrum oavsett värdet ändras. Det är användbart för avisering eftersom de kan samlas in ofta och en avisering kan vara drar igång snabbt med relativt enkla logik. Information om hur du skapar måttaviseringar finns i [skapa, visa och hantera aviseringar för mått med Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Media Services stöder övervakning mått för följande resurser:

* Konto
* Slutpunkten för direktuppspelning
 
### <a name="account"></a>Konto

Du kan övervaka följande konto mått. 

|Måttnamn|Visningsnamn|Beskrivning|
|---|---|---|
|AssetCount|Antal tillgångar|Tillgångar i ditt konto.|
|AssetQuota|Tillgången kvot|Tillgången kvot i ditt konto.|
|AssetQuotaUsedPercentage|Tillgången kvoten har använts procent|Procentandelen av kvoten för tillgången som redan används.|
|ContentKeyPolicyCount|Innehåll nyckel princip-antal|Principer för innehåll nycklar i ditt konto.|
|ContentKeyPolicyQuota|Innehåll nyckel princip kvot|Innehåll principer nycklar kvot i ditt konto.|
|ContentKeyPolicyQuotaUsedPercentage|Innehåll nyckel princip kvot används procent|Procentandelen av innehåll nyckel princip kvoten används redan.|
|StreamingPolicyCount|Princip för antal för direktuppspelning|Strömmande principer i ditt konto.|
|StreamingPolicyQuota|Strömning princip kvot|Direktuppspelning principer kvoten i ditt konto.|
|StreamingPolicyQuotaUsedPercentage|Strömmande princip kvot används procent|Procentandelen av kvoten för direktuppspelning principen används redan.|
 
Du bör också läsa igenom [konto kvoter och begränsningar](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Slutpunkten för direktuppspelning

Följande Media Services [Strömningsslutpunkter](https://docs.microsoft.com/rest/api/media/streamingendpoints) mått som stöds:

|Måttnamn|Visningsnamn|Beskrivning|
|---|---|---|
|Begäranden|Begäranden|Innehåller det totala antalet HTTP-begäranden som hanteras av slutpunkt för direktuppspelning.|
|Egress|Egress|Det totala antalet utgående byte. Till exempel byte som strömmas av slutpunkt för direktuppspelning.|
|SuccessE2ELatency|Svarstid för lyckad från slutpunkt till slutpunkt|Tidslängd från när den slutpunkt för direktuppspelning emot begäran till när den sista byten av svaret har skickats.|

### <a name="why-would-i-want-to-use-metrics"></a>Varför skulle jag vilja använda mått? 

Här följer exempel på hur övervakning av mått för Media Services kan hjälpa dig att förstå hur dina program utför. Några frågor som kan åtgärdas med Media Services-mått är:

* Hur övervakar jag min Standard-slutpunkt för direktuppspelning veta när jag har överskridit gränserna?
* Hur vet jag om jag har tillräckligt med skalningsenheter för Premium-slutpunkt för direktuppspelning? 
* Hur ställer jag en avisering när den att skala upp min slutpunkter för direktuppspelning?
* Hur ställer jag en avisering ska veta när max utgående som konfigurerats på kontot uppnåddes?
* Hur kan jag se fördelningen av begäranden som misslyckas och vad som orsakar felet?
* Hur kan jag se hur många begäranden för HLS eller DASH hämtas från Paketeraren?
* Hur ställer jag en avisering ska veta när tröskelvärdet för antal misslyckade begäranden träffades? 

### <a name="example"></a>Exempel

Se [så här övervakar du Media Services-mått](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Media Services-diagnostikloggar

Diagnostikloggar innehåller omfattande, frekventa data om användningen av en Azure-resurs. Mer information finns i [hur att samla in och använda loggdata från resurserna i Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Media Services stöder följande diagnostiska loggar:

* Leverans av nyckel

### <a name="key-delivery"></a>Leverans av nyckel

|Namn|Beskrivning|
|---|---|
|Viktiga leverans-tjänstbegäran|Loggar som visar nyckelleveranstjänst begär information. Mer information finns i [scheman](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Varför skulle jag vilja använda diagnostikloggar? 

Det finns några saker som du kan undersöka med nyckeln leverans diagnostikloggar:

* Se hur många licenser som levereras av DRM-typ
* Se hur många licenser som levereras av en princip 
* Se fel av typen DRM eller princip
* Se hur många obehörig licensbegäranden från klienter

### <a name="example"></a>Exempel

Se [övervaka Media Service-diagnostikloggar](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>Nästa steg 

* [Så här att samla in och använda loggdata från resurserna i Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Skapa, visa och hantera aviseringar för mått med Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Så här övervakar du Media Services-mått](media-services-metrics-howto.md)
* [Så här övervakar du Media Service-diagnostikloggar](media-services-diagnostic-logs-howto.md)
