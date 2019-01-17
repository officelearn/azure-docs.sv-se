---
title: Profilera live-Azure Cloud Services med Programinsikter | Microsoft Docs
description: Aktivera Application Insights Profiler för Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 01147f19a6a10361609c01bc6b3f1ac07d1ff86b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358040"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilera live-Azure Cloud Services med Programinsikter

Du kan också distribuera Application Insights Profiler på de här tjänsterna:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-program](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler har installerats med Azure Diagnostics-tillägget. Du behöver bara konfigurera Azure Diagnostics för att installera Profiler och skicka profiler till Application Insights-resursen.

## <a name="enable-profiler-for-azure-cloud-services"></a>Aktivera Profiler för Azure Cloud Services
1. Kontrollera att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare. Det räcker att bekräfta att den *ServiceConfiguration.\*.cscfg* filer har en `osFamily` värdet av ”5” eller senare.

1. Lägg till [Application Insights SDK till Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

1. Spåra begäranden med Application Insights:

    * För ASP.NET web-roller, kan Application Insights spåra begäranden automatiskt.

    * För arbetsroller, [lägger du till kod för att spåra begäranden](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Konfigurera Azure Diagnostics-tillägget för att aktivera Profiler genom att göra följande:

    a. Leta upp den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* filen för din programroll som visas här:  

      ![Platsen för konfigurationsfilen diagnostik](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Om du hittar filen [konfigurera diagnostik för Azure Cloud Services och virtuella datorer](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    b. Lägg till följande `SinksConfig` avsnittet som ett underordnat element av `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Om den *diagnostics.wadcfgx* -filen innehåller också en annan mottagare av typen ApplicationInsights, alla tre av följande instrumenteringsnycklar måste matcha:  
    > * Den nyckel som används av ditt program. 
    > * Den nyckel som används av ApplicationInsights mottagare. 
    > * Den nyckel som används av ApplicationInsightsProfiler mottagare. 
    >
    > Du kan hitta faktiska instrumentation nyckelvärdet som används av den `ApplicationInsights` mottagare den *ServiceConfiguration.\*.cscfg* filer. 
    > När Visual Studio 15.5 Azure SDK-versionen måste de instrumenteringsnycklar som används av programmet och ApplicationInsightsProfiler mottagare så att de matchar varandra.

1. Distribuera din tjänst med den nya konfigurationen Diagnostics och Application Insights Profiler är konfigurerad för att köras på din tjänst.
 
## <a name="next-steps"></a>Nästa steg

* Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
* Se [Profiler-spårningar](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
* Felsökning av problem med Profiler finns i [Profiler felsökning](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
