---
title: API-referens för Azure Application Insights-agent
description: Api-referens för Application Insights Agent. Hämta programInightsMonitoringConfig. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0cb5991b381acc9a0ec36113d0e15a7731b5074d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537499"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Program Insights Agent API: Hämta-ApplicationInsightsMonitoringConfig

I den här artikeln beskrivs en cmdlet som är medlem i [modulen Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Hämtar konfigurationsfilen och skriver ut värdena till konsolen.

> [!IMPORTANT] 
> Den här cmdleten kräver en PowerShell-session med administratörsbehörighet.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametrar

Inga parametrar krävs.

## <a name="output"></a>Resultat


#### <a name="example-output-from-reading-the-config-file"></a>Exempel på utdata från att läsa konfigurationsfilen

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Nästa steg

  Visa telemetrin:
 - [Utforska mätvärden](../../azure-monitor/platform/metrics-charts.md) för att övervaka prestanda och användning.
- [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- Använd [analyser](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till webbklienttelemetri](../../azure-monitor/app/javascript.md) för att se undantag från webbsidans kod och aktivera spårningsanrop.
- [Lägg till SDK för programinsikter i koden](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings- och loggsamtal.
 
 Gör mer med Application Insights Agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.
 - Gör ändringar i config med hjälp av [Set config](status-monitor-v2-api-set-config.md) cmdlet.
