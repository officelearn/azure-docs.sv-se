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
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: e4ba6cca679ce4910ea941d9578939721514b2ec
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478963"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure waf övervakning och loggning 

Azure web application firewall (WAF) övervakning och loggning sker via loggning och integrering med Azure Monitor och Azure Monitor loggar.

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

