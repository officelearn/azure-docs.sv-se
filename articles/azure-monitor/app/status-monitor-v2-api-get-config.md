---
title: API-referens för Azure Application Insights-agent
description: Application Insights Agent-API-referens. Get-ApplicationInsightsMonitoringConfig. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 91e7bcbe79dfde8bbd4011748b40b692015af1c2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899740"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Application Insights Agent-API: get-ApplicationInsightsMonitoringConfig

Den här artikeln beskriver en cmdlet som är medlem i [PowerShell-modulen AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Hämtar konfigurations filen och skriver ut värdena till-konsolen.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametrar

Inga parametrar krävs.

## <a name="output"></a>Resultat


#### <a name="example-output-from-reading-the-config-file"></a>Exempel på utdata från att läsa konfigurations filen

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Nästa steg

  Visa telemetrin:
 - [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- Använd [Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrument paneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) för att se till att din webbplats hålls Live.
- [Lägg till telemetri för webb klienter](../../azure-monitor/app/javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
- [Lägg till Application Insights SDK till din kod](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings-och logg anrop.
 
 Gör mer med Application Insights agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.
 - Gör ändringar i konfigurationen med hjälp av cmdleten [set config](status-monitor-v2-api-set-config.md) .
