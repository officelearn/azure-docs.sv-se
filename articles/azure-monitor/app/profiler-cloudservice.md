---
title: Profil live Azure Cloud Services med application insights | Microsoft-dokument
description: Aktivera Application Insights Profiler för Azure Cloud Services.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3fbeb1120e97a884135cd4622a49ef97fd43e58e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671672"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profil live Azure Cloud Services med application insights

Du kan också distribuera Application Insights Profiler på dessa tjänster:
* [Azure App-tjänst](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-program](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuella Azure-datorer](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler installeras med Azure Diagnostics-tillägget. Du behöver bara konfigurera Azure Diagnostics för att installera Profiler och skicka profiler till din Application Insights-resurs.

## <a name="enable-profiler-for-azure-cloud-services"></a>Aktivera Profiler för Azure Cloud Services
1. Kontrollera att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller nyare. Om du använder OS-familjen 4 måste du installera .NET Framework 4.6.1 eller nyare med en [startuppgift](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet). OS Family 5 innehåller som standard en kompatibel version av .NET Framework. 

1. Lägg till [Programstatistik SDK i Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Felet i profileren som levereras i WAD för molntjänster har åtgärdats.** Den senaste versionen av WAD (1.12.2.0) för Molntjänster fungerar med alla senaste versioner av App Insights SDK. Cloud Service-värdar uppgraderar WAD automatiskt, men det är inte omedelbart. Om du vill tvinga fram en uppgradering kan du distribuera om tjänsten eller starta om noden.

1. Spåra begäranden med Application Insights:

    * För ASP.NET webbroller kan Application Insights spåra begäranden automatiskt.

    * Lägg till [kod för att spåra begäranden för arbetsroller.](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

1. Konfigurera Azure Diagnostics-tillägget för att aktivera Profiler:

    a. Leta reda på [azure diagnostics diagnostics.wadcfgx-filen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* för din programroll, som du ser här:  

      ![Placering av diagnostikkonfigurationsfilen](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Om du inte hittar filen läser du [Konfigurera diagnostik för Azure Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Lägg till `SinksConfig` följande avsnitt som `WadCfg`ett underordnat element i :  

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
    > Om *filen diagnostics.wadcfgx* också innehåller en annan diskho av typen ApplicationInsights måste alla tre instrumentnycklarna matcha:  
    > * Nyckeln som används av ditt program. 
    > * Nyckeln som används av ApplicationInsights-diskhon. 
    > * Nyckeln som används av ApplicationInsightsProfiler-diskhon. 
    >
    > Du hittar det faktiska instrumenteringsnyckelvärdet `ApplicationInsights` som används av diskhon i *\*ServiceConfiguration. . cscfg-filer.* 
    > Efter Visual Studio 15.5 Azure SDK-versionen behöver endast instrumenteringsnycklarna som används av programmet och ApplicationInsightsProfiler-diskbänken matcha varandra.

1. Distribuera din tjänst med den nya diagnostikkonfigurationen och Application Insights Profiler är konfigurerad för att köras på din tjänst.
 
## <a name="next-steps"></a>Nästa steg

* Generera trafik till ditt program (till exempel starta ett [tillgänglighetstest).](monitor-web-app-availability.md) Vänta sedan 10 till 15 minuter på att spårningar ska börja skickas till application insights-instansen.
* Se [Profiler-spårningar](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
* Mer om du vill felsöka profilproblem finns i [Felsökning av Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
