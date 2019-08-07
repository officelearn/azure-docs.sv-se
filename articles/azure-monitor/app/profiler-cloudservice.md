---
title: Profilera Live Azure-Cloud Services med Application Insights | Microsoft Docs
description: Aktivera Application Insights Profiler för Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 93392e379cbb03508fefc1877d5d50e04436b79c
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737216"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilera Live Azure-Cloud Services med Application Insights

Du kan också distribuera Application Insights Profiler på dessa tjänster:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-program](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler installeras med Azure-diagnostik-tillägget. Du behöver bara konfigurera Azure-diagnostik för att installera profiler och skicka profiler till din Application Insights-resurs.

## <a name="enable-profiler-for-azure-cloud-services"></a>Aktivera profiler för Azure Cloud Services
1. Kontrol lera att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare. Om du använder OS-familjen 4 måste du installera .NET Framework 4.6.1 eller senare med en [Start aktivitet](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-install-dotnet). OS-familjen 5 innehåller en kompatibel version av .NET Framework som standard. 

1. Lägg till [Application Insights SDK i Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Felet i profileraren som levereras i WAD för Cloud Services har åtgärd ATS.** Den senaste versionen av WAD (1.12.2.0) för Cloud Services fungerar med alla nya versioner av App Insights SDK. Moln tjänst värdar kommer att uppgradera WAD automatiskt, men det är inte omedelbart. Om du vill framtvinga en uppgradering kan du distribuera om tjänsten eller starta om noden.

1. Spåra begär Anden med Application Insights:

    * Application Insights kan spåra begär Anden automatiskt för ASP.NET-webbroller.

    * För arbets roller [lägger du till kod för att spåra begär Anden](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Konfigurera Azure-diagnostik-tillägget för att aktivera profiler:

    a. Leta upp filen [Azure-diagnostik](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *Diagnostics. wadcfgx* för program rollen, som du ser här:  

      ![Platsen för konfigurations filen för diagnostik](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Om du inte hittar filen går du [till Konfigurera diagnostik för Azure Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Lägg till följande `SinksConfig` avsnitt som underordnat element `WadCfg`till:  

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
    > Om filen *Diagnostics. wadcfgx* också innehåller en annan mottagare av typen ApplicationInsights, måste alla tre av följande Instrumentation-nycklar matcha:  
    > * Den nyckel som används av ditt program. 
    > * Nyckeln som används av ApplicationInsights-mottagaren. 
    > * Nyckeln som används av ApplicationInsightsProfiler-mottagaren. 
    >
    > Du kan hitta faktiska instrumentation nyckelvärdet som används av den `ApplicationInsights` mottagare den *ServiceConfiguration.\*.cscfg* filer. 
    > Efter versionen av Visual Studio 15,5 Azure SDK måste bara de Instrumentation-nycklar som används av programmet och ApplicationInsightsProfiler-mottagare matcha varandra.

1. Distribuera tjänsten med den nya konfigurationen av diagnostik och Application Insights Profiler konfigureras för att köras på din tjänst.
 
## <a name="next-steps"></a>Nästa steg

* Generera trafik till ditt program (till exempel starta ett [tillgänglighets test](monitor-web-app-availability.md)). Vänta sedan 10 till 15 minuter så att spårningar börjar skickas till Application Insights-instansen.
* Se [profilerade spår](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure Portal.
* Information om fel sökning av profilerings problem finns i [fel sökning av](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)profileraren.
