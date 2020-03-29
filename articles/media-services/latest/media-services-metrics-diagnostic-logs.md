---
title: Media Services-mått och diagnostikloggar med Azure Monitor
titleSuffix: Azure Media Services
description: Lär dig hur du övervakar Azure Media Services-mått och diagnostikloggar via Azure Monitor.
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
ms.openlocfilehash: f075362f976e6abb26c9781c4b0cdeb7912c0862
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514042"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Övervaka Media Services-mått och diagnostikloggar via Azure Monitor

[Med Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina appar fungerar. Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer: mått och loggar. Du kan övervaka Diagnostikloggar för Media Services och skapa aviseringar och meddelanden för insamlade mått och loggar. Du kan visualisera och analysera måttdata med hjälp av [Statistikutforskaren](../../azure-monitor/platform/metrics-getting-started.md). Du kan skicka loggar till [Azure Storage,](https://azure.microsoft.com/services/storage/)strömma dem till [Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/)exportera dem till [Log Analytics](https://azure.microsoft.com/services/log-analytics/)eller använda tjänster från tredje part.

En detaljerad översikt finns i [Azure Monitor Metrics](../../azure-monitor/platform/data-platform.md) och [Azure Monitor Diagnostic logs](../../azure-monitor/platform/platform-logs-overview.md).

I det här avsnittet [beskrivs medietjänstmått](#media-services-metrics) och [diagnostikloggar för Media Services](#media-services-diagnostic-logs)som stöds.

## <a name="media-services-metrics"></a>Media Services-mått

Mått samlas in med jämna mellanrum oavsett om värdet ändras eller inte. De är användbara för aviseringar eftersom de kan samplas ofta, och en avisering kan avfyras snabbt med relativt enkel logik. Information om hur du skapar måttaviseringar finns i [Skapa, visa och hantera måttaviseringar med Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Media Services stöder övervakningsmått för följande resurser:

* Konto
* Slutpunkt för direktuppspelning

### <a name="account"></a>Konto

Du kan övervaka följande kontomått.

|Måttnamn|Visningsnamn|Beskrivning|
|---|---|---|
|Tillgångsräkning|Antal tillgångar|Tillgångar på ditt konto.|
|TillgångQuota|Tillgångskvot|Tillgångskvot i ditt konto.|
|TillgångQuotaUsedPercentage|Använd procentandel för tillgångskvot|Den procentandel av tillgångskvoten som redan har använts.|
|InnehållKeyPolicyCount|Antal principer för innehållsnyckel|Principer för innehållsnyckel i ditt konto.|
|ContentKeyPolicyQuota|Policykvot för innehållsnyckel|Kvoten för innehållsnyckelprinciper i ditt konto.|
|ContentKeyPolicyQuotaUsedPercentage|Procentuell kvot för innehållsnyckelprincip|Procentandelen av den principkvot för innehållsnyckel som redan har använts.|
|StreamingPolicyCount|Antal streamingprinciper|Streamingpolicyer i ditt konto.|
|StreamingPolicyQuota|Kvot för direktuppspelningsprincip|Kvoten För streamingprinciper i ditt konto.|
|StreamingPolicyQuotaUsedPercentage|Datauppspelningsprincipkvot använd procent|Procentandelen av den direktuppspelningsprincipkvot som redan har använts.|

Du bör också granska [kontokvoter och begränsningar](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Slutpunkt för direktuppspelning

Följande mätvärden för direktuppspelning av [medietjänster](https://docs.microsoft.com/rest/api/media/streamingendpoints) stöds:

|Måttnamn|Visningsnamn|Beskrivning|
|---|---|---|
|Begäranden|Begäranden|Anger det totala antalet HTTP-begäranden som betjänas av slutpunkten för direktuppspelning.|
|Utgående|Utgående|Det totala antalet utgående byte. Byte som strömmas av slutpunkten för direktuppspelning.|
|FramgångE2ELatency|Lyckades avsluta svarstiden|Tidsvaraktighet från det att slutpunkten för direktuppspelning tog emot begäran till när den sista bytet av svaret skickades.|

### <a name="why-would-i-want-to-use-metrics"></a>Varför skulle jag vilja använda mått?

Här är exempel på hur övervakning av Media Services-mätvärden kan hjälpa dig att förstå hur dina appar fungerar. Några frågor som kan åtgärdas med Media Services-mått är:

* Hur övervakar jag min standardströmavslutpunkt för direktuppspelning för att veta när jag har överskridit gränserna?
* Hur vet jag om jag har tillräckligt med Premium Streaming Endpoint-skalningsenheter?
* Hur kan jag ställa in en avisering så att den vet när mina slutpunkter för direktuppspelning ska skalas upp?
* Hur ställer jag in en avisering för att veta när den maxutgång som konfigurerats för kontot uppnåddes?
* Hur kan jag se att uppdelningen av begäranden misslyckas och vad som orsakar felet?
* Hur kan jag se hur många HLS- eller DASH-begäranden som hämtas från förpackaren?
* Hur ställer jag in en avisering för att veta när tröskelvärdet för # för misslyckade begäranden träffades?

### <a name="example"></a>Exempel

Se [Så här övervakar du Media Services-mått](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Diagnostikloggar för Media Services

Diagnostikloggar ger omfattande och frekventa data om driften av en Azure-resurs. Mer information finns i Så här samlar du [in och använder loggdata från dina Azure-resurser](../../azure-monitor/platform/platform-logs-overview.md).

Media Services stöder följande diagnostikloggar:

* Nyckelleverans

### <a name="key-delivery"></a>Nyckelleverans

|Namn|Beskrivning|
|---|---|
|Begäran om nyckelleveranstjänst|Loggar som visar information om nyckelleveransservicebegäranden. Mer information finns i [scheman](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Varför skulle jag vilja använda diagnostikloggar?

Några saker som du kan undersöka med viktiga leveransdiagnostiska loggar är:

* Se antalet licenser som levereras av DRM-typ.
* Se antalet licenser som levereras av principen.
* Se fel efter DRM eller principtyp.
* Se antalet obehöriga licensbegäranden från klienter.

### <a name="example"></a>Exempel

Se [Så här övervakar du diagnostikloggar för Medietjänsten](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Nästa steg

* [Så här samlar du in och använder loggdata från dina Azure-resurser](../../azure-monitor/platform/platform-logs-overview.md)
* [Skapa, visa och hantera måttaviseringar med Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Så här övervakar du Media Services-mått](media-services-metrics-howto.md)
* [Så här övervakar du diagnostikloggar för Media Service](media-services-diagnostic-logs-howto.md)
