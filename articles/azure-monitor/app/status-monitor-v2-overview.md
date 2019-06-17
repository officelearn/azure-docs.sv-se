---
title: Azure statusövervakaren v2-översikt | Microsoft Docs
description: En översikt över statusövervakaren v2. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734161"
---
# <a name="status-monitor-v2"></a>Statusövervakare v2

Status Monitor v2 är en PowerShell module publiceras till den [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Det ersätter [statusövervakaren](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Modulen innehåller kodlös instrumentering av .NET-webbappar som körs med IIS.
Telemetri skickas till Azure-portalen, där du kan [övervakaren](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) din app.

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>PowerShell-galleriet

PowerShell-galleriet finns här: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


## <a name="instructions"></a>Instruktioner
- Se den [komma igång instruktioner](status-monitor-v2-get-started.md) att hämta en start med kortfattade kodexempel.
- Se den [detaljerade instruktioner](status-monitor-v2-detailed-instructions.md) för djupgående information om hur du kommer igång.

## <a name="powershell-api-reference"></a>PowerShell-API-referens
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Inaktivera InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Aktivera InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Felsökning
- [Felsökning](status-monitor-v2-troubleshoot.md)
- [Kända problem](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

- Stöder statusövervakaren v2 proxy installationer?

  *Ja*. Det finns flera sätt att hämta Status Monitor v2. Om datorn är ansluten till internet, kan du publicera PowerShell-galleriet med hjälp av `-Proxy` parametrar.
Du kan också manuellt hämta modulen och installera den på datorn eller använda det direkt.
Var och en av dessa alternativ beskrivs i den [detaljerade instruktioner](status-monitor-v2-detailed-instructions.md).
  
- Hur bekräftar jag att operatörshanterad lyckades?

   Det finns inga cmdlet för att kontrollera aktivering har utförts.
Vi rekommenderar att du använder [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) snabbt bedöma om din app skickar telemetri.

   Du kan också använda [Log Analytics](../log-query/get-started-portal.md) att lista alla molnroller som skickar telemetri:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Nästa steg

Visa telemetrin:

* [Utforska mått](../../azure-monitor/app/metrics-explorer.md) att övervaka prestanda och användning.
* [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) att diagnostisera problem.
* [Använda Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
* [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).

Lägg till mer telemetri:

* [Skapa webbtester](monitor-web-app-availability.md) att kontrollera att webbplatsen är aktiv.
* [Lägg till telemetri för webbklienten](../../azure-monitor/app/javascript.md) vill visa undantag från webbsidans kod och för att aktivera spårning av anrop.
* [Lägg till Application Insights SDK i koden](../../azure-monitor/app/asp-net.md) så att du kan lägga till spårnings- och logganrop.

