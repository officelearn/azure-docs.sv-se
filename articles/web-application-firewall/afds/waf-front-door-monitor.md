---
title: Övervakning och loggning av brand vägg för Azure-webbprogram
description: Lär dig mer om brand vägg för webbaserade program (WAF) med ytterdörr-övervakning och loggning
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 3446df6effd85a07beb463c1caa40c5826a9e019
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934695"
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

Här är ett exempel på en loggad begäran i WAF-loggen:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

I följande exempel fråga hämtas AccessLogs-poster:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Här är ett exempel på en loggad begäran i Access logg:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [front dörren](../../frontdoor/front-door-overview.md).