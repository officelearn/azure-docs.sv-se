---
title: Övervakning och loggning av brand vägg för Azure-webbprogram
description: Lär dig mer om brand vägg för webbaserade program (WAF) med ytterdörr-övervakning och loggning
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512488"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Övervakning och loggning av brand vägg för Azure-webbprogram 

Övervakning och loggning av Azure Web Application-brandväggen (WAF) tillhandahålls genom loggning och integrering med Azure Monitor-och Azure Monitor-loggar.

## <a name="azure-monitor"></a>Azure Monitor

WAF med ytterdörr-loggen är integrerad med [Azure Monitor](../../azure-monitor/overview.md). Med Azure Monitor kan du spåra diagnostikinformation, inklusive WAF-aviseringar och loggar. Du kan konfigurera WAF övervakning i den främre dörren i portalen under fliken **diagnostik** eller via tjänsten Azure Monitor direkt.

Från Azure Portal går du till resurs typen frontend-dörr. Från **övervakning**/fliken **mått** till vänster kan du lägga till **WebApplicationFirewallRequestCount** för att spåra antalet förfrågningar som matchar WAF-regler. Anpassade filter kan skapas baserat på åtgärds typer och regel namn.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Loggar och diagnostik

WAF med front dörren ger detaljerad rapportering om varje hot som identifieras. Loggningen är integrerad med loggar och varningar för Azure Diagnostics och registreras i JSON-format. Dessa loggar kan integreras med [Azure Monitor loggar](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog loggar alla förfrågningar som vidarebefordras till kund Server delar. FrontdoorWebApplicationFirewallLog loggar alla förfrågningar som matchar en WAF-regel.

I följande exempel fråga hämtas WAF-loggar på blockerade begär Anden:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

I följande exempel fråga hämtas AccessLogs-poster:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [front dörren](../../frontdoor/front-door-overview.md).

