---
title: Med Azure Log Analytics kan du granska loggarna för Application Gateway Web Application Firewall
description: Den här artikeln visar hur du kan använda Azure Log Analytics för att granska loggarna för Application Gateway Web Application Firewall
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712181"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Använd Log Analytics för att undersöka brandväggsloggar för Application Gateway Web Application

När Application Gateway WAF är i drift, kan du aktivera loggar granska vad som händer med varje begäran. Brandväggen loggar ger insikt i vilka WAF utvärderar matchar och blockerar. Du kan undersöka data i loggarna från brandväggen för att ge ytterligare insikter med Log Analytics. Läs mer om hur du skapar en Log Analytics-arbetsyta, [skapa en Log Analytics-arbetsyta i Azure-portalen](../azure-monitor/learn/quick-create-workspace.md). Läs mer om loggfrågor [översikt över log frågor i Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importera WAF-loggar

Om du vill importera dina loggar från brandväggen till Log Analytics, se [backend-hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md#diagnostic-logging). När du har brandväggsloggar i Log Analytics-arbetsytan kan du visa data, skriva frågor, skapa visualiseringar och lägga till dem i instrumentpanelen i portalen.

## <a name="explore-data-with-examples"></a>Utforska data med exempel

Om du vill visa rådata i brandväggsloggen för, kan du köra följande fråga:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Detta ser ut ungefär som följande fråga:

![Log Analytics-fråga](media/log-analytics/log-query.png)

Du kan granska nedåt i data, och rita diagram eller skapa visualiseringar härifrån. Se följande frågor som utgångspunkt:

### <a name="matchedblocked-requests-by-ip"></a>Matchade/blockerad begäranden efter IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Matchade/blockerad begäranden av URI: N

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Främsta matchade reglerna

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Översta fem matchande regelgrupper

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Lägga till i instrumentpanelen

När du skapar en fråga kan du lägga till den på instrumentpanelen.  Välj den **fäst på instrumentpanelen** uppe till höger i log analytics-arbetsytan. Med de föregående fyra fästa på en instrumentpanel med exempel, är detta de data som du kan se en snabb överblick över:

![Instrumentpanel](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Nästa steg

[Backend-hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md)
