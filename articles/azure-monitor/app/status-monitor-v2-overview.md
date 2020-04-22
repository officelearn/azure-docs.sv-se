---
title: Översikt över Azure Application Insights Agent | Microsoft-dokument
description: En översikt över Application Insights Agent. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770970"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Distribuera Azure Monitor Application Insights Agent för lokala servrar

> [!IMPORTANT]
> Den här vägledningen rekommenderas för lokala och icke-Azure-molndistributioner av Application Insights Agent. Här är den rekommenderade metoden för [Azure virtuell dator och virtuell dator skala uppsättning distributioner](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights Agent (tidigare namnet Status Monitor V2) är en PowerShell-modul som publicerats i [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Den ersätter [statusövervakare](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Telemetri skickas till Azure-portalen, där du kan [övervaka](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) din app.

> [!NOTE]
> Modulen stöder för närvarande endast kodlös instrumentering av .NET-webbappar som är värd för IIS. Använd ett SDK-instrument för att instrumentera ASP.NET Core-, Java- och Node.js-program.

## <a name="powershell-gallery"></a>PowerShell-galleriet

Application Insights Agent finns https://www.powershellgallery.com/packages/Az.ApplicationMonitorhär: .

![PowerShell-galleriet](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instruktioner
- Se [instruktionerna](status-monitor-v2-get-started.md) för att komma igång för att få en start med koncisa kodexempel.
- Se [detaljerade instruktioner](status-monitor-v2-detailed-instructions.md) för en djupdykning om hur du kommer igång.

## <a name="powershell-api-reference"></a>PowerShell-API-referens
- [Inaktivera-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [Inaktivera instrumenteringsengine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [Aktivera-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [Aktivera instrumenteringEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [Hämta programInightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [Hämta programInightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [Start-ProgramInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Felsökning
- [Felsökning](status-monitor-v2-troubleshoot.md)
- [Kända problem](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

- Stöder Application Insights Agent proxyinstallationer?

  *- Ja,* det är jag. Det finns flera sätt att hämta Application Insights Agent. Om datorn har tillgång till internet kan du gå `-Proxy` ombord till PowerShell-galleriet med hjälp av parametrar.
Du kan också hämta modulen manuellt och antingen installera den på datorn eller använda den direkt.
Vart och ett av dessa alternativ beskrivs i de [detaljerade instruktionerna](status-monitor-v2-detailed-instructions.md).

- Stöder Status Monitor v2 ASP.NET Core-program?

  *Nej, det är jag*inte. Instruktioner för hur du aktiverar övervakning av ASP.NET Core-program finns i [Application Insights för ASP.NET Core-program](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Det finns ingen anledning att installera StatusMonitor för en ASP.NET Core-program. Detta gäller även om ASP.NET Core-programmet finns i IIS.

- Hur verifierar jag att aktiveringen lyckades?

  - [Cmdlet Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) kan användas för att verifiera att enablement lyckades.
  - Vi rekommenderar att du använder [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) för att snabbt avgöra om din app skickar telemetri.

  - Du kan också använda [Log Analytics](../log-query/get-started-portal.md) för att lista alla molnroller som för närvarande skickar telemetri:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Nästa steg

Visa telemetrin:

* [Utforska mätvärden](../../azure-monitor/platform/metrics-charts.md) för att övervaka prestanda och användning.
* [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
* [Använd Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
* [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).

Lägg till mer telemetri:

* [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
* [Lägg till webbklienttelemetri](../../azure-monitor/app/javascript.md) för att se undantag från webbsidans kod och aktivera spårningsanrop.
* [Lägg till SDK för programinsikter i koden](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings- och loggsamtal.

