---
title: Profilera live-Azure cloud services med Programinsikter | Microsoft Docs
description: Aktivera Application Insights Profiler för molntjänster.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 445e607b6b0a21f840ab633b3a5a3779f49fdd98
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142503"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilera live-Azure cloud services med Programinsikter

Du kan också distribuera Application Insights profiler på de här tjänsterna:
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-program](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler har installerats med Windows Azure Diagnostics SÄKERHETSSPECIFIKA-tillägget. Du behöver bara konfigurera WAD för att installera profiler och skicka profiler till Application Insights-resursen.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Aktivera profiler för din Azure-molntjänst
1. Kontrollera att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare.  Det räcker att bekräfta att den *ServiceConfiguration.\*. cscfg* filer har en `osFamily` värdet av ”5” eller senare.
1. Lägg till [Application Insights SDK till Molntjänsten](app-insights-cloudservices.md?toc=/azure/azure-monitor/toc.json).
1. Spåra begäranden med Application Insights:

    För ASP.Net web-roller, kan Application Insights spåra begäranden automatiskt.

    För arbetsroller, [lägger du till kod för att spåra begäranden.](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. Konfigurera Windows Azure Diagnostics SÄKERHETSSPECIFIKA tillägget om du vill aktivera profiler.



    1. Leta upp den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* filen för din programroll som visas här:  

       ![Platsen för konfigurationsfilen diagnostik](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        Om du inte hittar filen, information om hur du aktivera tillägget diagnostik i Azure Cloud Services-projektet finns [konfigurera diagnostik för Azure Cloud Services och virtuella datorer](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    1. Lägg till följande `SinksConfig` avsnittet som ett underordnat element av `WadCfg`:  

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

    >   **Obs:** om den *diagnostics.wadcfgx* -filen innehåller också en annan mottagare av typen `ApplicationInsights`, alla tre av följande instrumenteringsnycklar måste matcha:  
    >  * Den nyckel som används av ditt program.  
    >  * Den nyckel som används av den `ApplicationInsights` mottagare.  
    >  * Den nyckel som används av den `ApplicationInsightsProfiler` mottagare.  
    >
    > Du kan hitta faktiska instrumentation nyckelvärdet som används av den `ApplicationInsights` mottagare den *ServiceConfiguration.\*. cscfg* filer.  
    > När du har Visual Studio 15.5 Azure SDK-versionen kan endast instrumenteringsnycklar som används av programmet och `ApplicationInsightsProfiler` mottagare behöver matchar varandra.
1. Distribuera din tjänst med den nya diagnostiska konfigurationen och Application Insights Profiler kommer att konfigureras för att köras på din tjänst.
 
## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
- Se [Profiler-spårningar](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
- Få hjälp med felsökning av problem med profiler i [Profiler felsökning](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).