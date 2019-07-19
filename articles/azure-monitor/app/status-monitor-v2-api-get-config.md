---
title: 'Azure Statusövervakare v2 API-referens: Hämta konfiguration | Microsoft Docs'
description: API-referens för Statusövervakare v2. Get-ApplicationInsightsMonitoringConfig. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 9e1b3242a96bfaadfe6f791e0ca9bf25262065e2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326339"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig"></a>API för Statusövervakare v2: Get-ApplicationInsightsMonitoringConfig

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

## <a name="output"></a>Output


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
 - [Skapa](monitor-web-app-availability.md) webbtester för att se till att din webbplats hålls Live.
- [Lägg till telemetri för webb klienter](../../azure-monitor/app/javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
- [Lägg till Application Insights SDK till din kod](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings-och logg anrop.
 
 Gör mer med Statusövervakare v2:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) statusövervakare v2.
 - Gör ändringar i konfigurationen med hjälp av cmdleten [set config](status-monitor-v2-api-set-config.md) .
