---
title: Använda Azure Log Analytics för att undersöka brandväggsloggar för programgateway
description: Den här artikeln visar hur du kan använda Azure Log Analytics för att undersöka brandväggsloggar för programgateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: e1bc3b58f425b374e4cae1da6e9800579e503f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516596"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Använda Log Analytics för att undersöka loggar från brandväggen för webbaserade program för Application Gateway

När din Application Gateway WAF är i drift kan du aktivera loggar för att kontrollera vad som händer med varje begäran. Brandväggsloggar ger insikt i vad WAF utvärderar, matchar och blockerar. Med Log Analytics kan du undersöka data i brandväggsloggarna för att ge ännu fler insikter. Mer information om hur du skapar en Log Analytics-arbetsyta finns [i Skapa en log analytics-arbetsyta i Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md). Mer information om loggfrågor finns [i Översikt över loggfrågor i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importera WAF-loggar

Information om hur du importerar brandväggsloggarna till Log Analytics finns i [Backend-hälsa, diagnostikloggar och mått för Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging). När du har brandväggsloggarna på arbetsytan Log Analytics kan du visa data, skriva frågor, skapa visualiseringar och lägga till dem på portalinstrumentpanelen.

## <a name="explore-data-with-examples"></a>Utforska data med exempel

Om du vill visa rådata i brandväggsloggen kan du köra följande fråga:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Detta kommer att se ut ungefär som följande fråga:

![Fråga om logganalys](../media/log-analytics/log-query.png)

Du kan öka detaljnivån i data och rita diagram eller skapa visualiseringar härifrån. Se följande frågor som utgångspunkt:

### <a name="matchedblocked-requests-by-ip"></a>Matchade/blockerade begäranden via IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Matchade/blockerade begäranden av URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Toppmatchade regler

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Topp fem matchade regelgrupper

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Lägg till på instrumentpanelen

När du har skapat en fråga kan du lägga till den på instrumentpanelen.  Välj **fäst på instrumentpanelen** längst upp till höger på logganalysarbetsytan. Med de fyra föregående frågorna fästa på en exempelinstrumentpanel är detta de data som du snabbt kan se:

![Instrumentpanel](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Nästa steg

[Backend-hälsa, diagnostikloggar och mått för Application Gateway](../../application-gateway/application-gateway-diagnostics.md)