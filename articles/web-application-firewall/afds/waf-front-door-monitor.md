---
title: Övervakning och loggning av Azure Web Application-brandväggen
description: Lär dig brandvägg för webbprogram (WAF) med FrontDoor-övervakning och loggning
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284151"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Övervakning och loggning av Azure Web Application-brandväggen 

Övervakning och loggning (Azure Web Application Firewall) tillhandahålls genom loggning och integrering med Azure Monitor- och Azure Monitor-loggar.

## <a name="azure-monitor"></a>Azure Monitor

WAF med FrontDoor-loggen är integrerad med [Azure Monitor](../../azure-monitor/overview.md). Med Azure Monitor kan du spåra diagnostikinformation, inklusive WAF-aviseringar och loggar. Du kan konfigurera WAF-övervakning inom front door-resursen i portalen under fliken **Diagnostik** eller via Azure Monitor-tjänsten direkt.

Gå till resurstypen Ytterdörr från Azure-portalen. Från fliken/**Övervakningsmått** till vänster kan du lägga till **WebApplicationFirewallRequestCount** för att spåra antalet begäranden som matchar WAF-regler. **Monitoring** Anpassade filter kan skapas baserat på åtgärdstyper och regelnamn.

![WAFMetrics (19)100](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Loggar och diagnostik

WAF med ytterdörren ger detaljerad rapportering om varje hot den upptäcker. Loggningen är integrerad med loggar och varningar för Azure Diagnostics och registreras i JSON-format. Dessa loggar kan integreras med [Azure Monitor-loggar](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag (på andra sätt)](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog loggar alla begäranden som vidarebefordras till kundens back-ends. FrontdoorWebApplicationFirewallLog loggar alla förfrågningar som matchar en WAF-regel.

Följande exempelfråga hämtar WAF-loggar på blockerade begäranden:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

Här är ett exempel på en loggad begäran i WAF logg:

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

Följande exempelfråga hämtar AccessLogs-poster:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Här är ett exempel på en inloggad begäran i Access-loggen:

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

- Läs mer om [ytterdörren.](../../frontdoor/front-door-overview.md)
