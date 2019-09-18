---
title: Översikt över Azure Statusövervakare v2 | Microsoft Docs
description: En översikt över Statusövervakare v2. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: tilee
ms.openlocfilehash: 3060659c5f870be60f1ac02e432dd0a8333f0900
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057840"
---
# <a name="status-monitor-v2"></a>Statusövervakare v2

Statusövervakare v2 är en PowerShell-modul som publiceras på [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Den ersätter [statusövervakare](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Modulen tillhandahåller kod för kodbaserade instrumentering av .NET-webbappar som är värdbaserade i IIS.
Telemetri skickas till Azure Portal, där du kan [övervaka](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) din app.

## <a name="powershell-gallery"></a>PowerShell-galleriet

Statusövervakare v2 finns här: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell-galleriet](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Anvisningar
- Se [komma igång-instruktionerna](status-monitor-v2-get-started.md) för att få en start med kortfattade kod exempel.
- Se de [detaljerade anvisningarna](status-monitor-v2-detailed-instructions.md) för att få en djup introduktion till hur du kommer igång.

## <a name="powershell-api-reference"></a>PowerShell API-referens
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Aktivera – InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Felsökning
- [Felsökning](status-monitor-v2-troubleshoot.md)
- [Kända problem](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

- Stöder Statusövervakare v2 proxy-installationer?

  *Ja*. Det finns flera sätt att ladda ned Statusövervakare v2. Om datorn har Internet åtkomst kan du publicera den PowerShell-galleriet med hjälp `-Proxy` av parametrar.
Du kan också hämta modulen manuellt och antingen installera den på datorn eller använda den direkt.
Vart och ett av dessa alternativ beskrivs i de [detaljerade anvisningarna](status-monitor-v2-detailed-instructions.md).

- Stöder Statusövervakare v2 ASP.NET Core program?

  *Nej*. Instruktioner för hur du aktiverar övervakning av ASP.NET Core program finns i [Application Insights för ASP.net Core program](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Du behöver inte installera StatusMonitor för ett ASP.NET Core-program. Detta gäller även om ASP.NET Core program finns i IIS.
  
Stöder Statusövervakare v2 ASP.NET Core program? 

  *Nej*. Följ [dessa](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) anvisningar för att aktivera övervakning av ASP.net Core program. Du behöver inte installera StatusMonitor för ett ASP.NET Core-program. Detta gäller även om ASP.NET Core program finns i IIS.

- Hur gör jag för att verifiera att aktiveringen lyckades?

  - [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) -cmdlet: en kan användas för att kontrol lera att aktiveringen har slutförts.
  - Vi rekommenderar att du använder [Live-mått](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) för att snabbt avgöra om din app skickar telemetri.

  - Du kan också använda [Log Analytics](../log-query/get-started-portal.md) för att visa en lista över alla moln roller som för närvarande skickar telemetri:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Nästa steg

Visa telemetrin:

* [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
* [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
* [Använd Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
* [Skapa instrument paneler](../../azure-monitor/app/overview-dashboard.md).

Lägg till mer telemetri:

* [Skapa](monitor-web-app-availability.md) webbtester för att se till att din webbplats hålls Live.
* [Lägg till telemetri för webb klienter](../../azure-monitor/app/javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
* [Lägg till Application Insights SDK till din kod](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings-och logg anrop.

