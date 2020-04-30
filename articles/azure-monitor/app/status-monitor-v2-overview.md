---
title: Översikt över Azure Application Insights-agent | Microsoft Docs
description: En översikt över Application Insights-agenten. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770970"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Distribuera Azure Monitor Application Insights agent för lokala servrar

> [!IMPORTANT]
> Den här vägledningen rekommenderas för lokala och icke-Azure-moln distributioner av Application Insights agent. Här är den rekommenderade metoden för [distributioner av skalnings uppsättningar för virtuella Azure-datorer och virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights agent (tidigare namngiven Statusövervakare v2) är en PowerShell-modul som publiceras till [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Den ersätter [statusövervakare](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Telemetri skickas till Azure Portal, där du kan [övervaka](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) din app.

> [!NOTE]
> Modulen stöder för närvarande inte kodbaserade instrumentering av .NET-webbappar som är värdbaserade i IIS. Använd en SDK för att instrumentera ASP.NET Core-, Java-och Node. js-program.

## <a name="powershell-gallery"></a>PowerShell-galleriet

Application Insights agent finns här: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell-galleriet](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instruktioner
- Se [komma igång-instruktionerna](status-monitor-v2-get-started.md) för att få en start med kortfattade kod exempel.
- Se de [detaljerade anvisningarna](status-monitor-v2-detailed-instructions.md) för att få en djup introduktion till hur du kommer igång.

## <a name="powershell-api-reference"></a>PowerShell API-referens
- [Disable-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [Aktivera – ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [Aktivera – InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Felsökning
- [Felsökning](status-monitor-v2-troubleshoot.md)
- [Kända problem](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

- Stöder Application Insights-agenten proxy-installationer?

  *Ja*. Det finns flera sätt att ladda ned Application Insights-agenten. Om datorn har Internet åtkomst kan du publicera den PowerShell-galleriet med hjälp `-Proxy` av parametrar.
Du kan också hämta modulen manuellt och antingen installera den på datorn eller använda den direkt.
Vart och ett av dessa alternativ beskrivs i de [detaljerade anvisningarna](status-monitor-v2-detailed-instructions.md).

- Stöder Statusövervakare v2 ASP.NET Core program?

  *Nej*. Instruktioner för hur du aktiverar övervakning av ASP.NET Core program finns i [Application Insights för ASP.net Core program](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Du behöver inte installera StatusMonitor för ett ASP.NET Core-program. Detta gäller även om ASP.NET Core program finns i IIS.

- Hur gör jag för att verifiera att aktiveringen lyckades?

  - [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) -cmdlet: en kan användas för att kontrol lera att aktiveringen har slutförts.
  - Vi rekommenderar att du använder [Live-mått](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) för att snabbt avgöra om din app skickar telemetri.

  - Du kan också använda [Log Analytics](../log-query/get-started-portal.md) för att visa en lista över alla moln roller som för närvarande skickar telemetri:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Nästa steg

Visa telemetrin:

* [Utforska mått](../../azure-monitor/platform/metrics-charts.md) för att övervaka prestanda och användning.
* [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
* [Använd Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
* [Skapa instrument paneler](../../azure-monitor/app/overview-dashboard.md).

Lägg till mer telemetri:

* [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
* [Lägg till telemetri för webb klienter](../../azure-monitor/app/javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
* [Lägg till Application Insights SDK till din kod](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings-och logg anrop.

