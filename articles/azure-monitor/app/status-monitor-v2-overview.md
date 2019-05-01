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
ms.openlocfilehash: fac14281365ccf3c191684af8cfdebda69e734e0
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870454"
---
# <a name="status-monitor-v2"></a>Status Monitor v2

Status Monitor v2 är en PowerShell-modul som publiceras till den [PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) och är en ersättning för [statusövervakaren](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Den här modulen innehåller kod utan instrumentering av .NET-webbprogram med IIS.
Telemetri kommer att skickas till Azure-portalen där du kan [övervakaren](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) ditt program.

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>PowerShell-galleriet

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Instruktioner
- Granska våra [komma igång instruktioner](status-monitor-v2-get-started.md) och kom igång nu med kortfattade kodexempel.
- Granska våra [detaljerade instruktioner](status-monitor-v2-detailed-instructions.md) för djupgående information om hur du kommer igång.

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

  **Ja**. Du har flera alternativ för att ladda ner statusövervakaren v2. Om datorn är ansluten till internet, kan du lägga till PowerShell-galleriet med `-Proxy` parametrar. Du kan också ladda ned den här modulen och installera den på din dator eller använda modulen direkt. Var och en av dessa alternativ beskrivs i vår [detaljerade anvisningar](status-monitor-v2-detailed-instructions.md).
  
- Så här verifierar du operatörshanterad lyckades?

   Vi har inte en cmdlet för att verifiera att aktivering lyckades. Vi rekommenderar att du använder [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) att snabbt se om ditt program skickar oss telemetri.
