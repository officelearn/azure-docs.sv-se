---
title: Azure waf övervakning och loggning
description: Lär dig Brandvägg för webbaserade program (WAF) med FrontDoor övervakning och loggning
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363157"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure waf övervakning och loggning 

Azure web application firewall (WAF) övervakning och loggning sker via loggning och integrering med Azure Monitor och Azure Monitor loggar.

> [!IMPORTANT]
> WAF övervakning och loggning för Azure ytterdörren för funktionen är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="azure-monitor"></a>Azure Monitor

WAF med FrontDoor loggen är integrerad med [Azure Monitor](../azure-monitor/overview.md). Azure Monitor kan du spåra diagnostisk information, inklusive WAF varningar och loggar. Du kan konfigurera WAF-övervakning i ytterdörren resursen i portalen under den **diagnostik** fliken eller i Azure Monitor-tjänsten direkt.

Gå till ytterdörren resurstyp från Azure-portalen. Från **övervakning**/**mått** fliken till vänster kan du lägga till **WebApplicationFirewallRequestCount** att spåra antalet begäranden som matchar WAF-regler. Anpassade filter kan skapas utifrån åtgärdstyper och namn.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Loggar och diagnostik

WAF med ytterdörren erbjuder detaljerad rapportering för varje hot som upptäcks. Loggningen är integrerad med loggar och varningar för Azure Diagnostics och registreras i JSON-format. Dessa loggar kan integreras med [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog loggar alla begäranden som vidarebefordras till kund-servrar. FrontdoorWebApplicationFirewallLog loggar varje begäran som matchar en WAF-regel.

Följande exempelfråga hämtar WAF-loggfilerna på den blockerade begäranden:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Följande exempelfråga hämtar AccessLogs poster:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [ytterdörren](front-door-overview.md).

